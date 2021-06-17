


== header ===

##Fenwick một cấu trúc dữ liệu 
	- trích dẫn wiki về nguồn gốc và tác giả

> **NOTE:** Để thuận tiện và tránh nhầm lẫn thì mảng trong bài viết sẽ để sử dụng :

> - Mảng arr[200001] gồm 1e6 + 1 phần tử, 1-based indexing (các phần tử sẽ sử bắt đầu tại a[1] thay vì a[0] như thường lệ)

> - Fenwick cũng chỉ có thể sử dụng trên 1-based indexing.( thử tự hỏi sau khi đọc bài viết này nhé)

> - Để dễ quen thuộc về sau thì bài viết sẽ gọi các phần tử của mảng fw là node.

> - Bài viết sẽ sử dụng nhiều đến biểu diễn nhị phân, mình sẽ viết tắt như sau <biểu diễn hệ cơ số 10> (<biểu diễn hệ cơ số 2>)
>		vd :
			12 (1100) : có nghĩa là 12 và biểu diễn nhị phân của 12 là 1100
> - log(n) đương nhiên sẽ là logarit cơ số 2 của n.

## Nhắc lại về mảng tổng tiền tố, vì sao cần fenwick
 
Như ta đã biết mảng tổng tiền tố là một cấu trúc dữ liệu cho phép tính tổng các phần tử từ 1 đến một vị trí i bất kì, từ đó tính được tổng của một đoạn bất kì với độ phức tạp O(1).
mình hay sử dụng mảng tổng tiền tố như sau :


    int const n = 2e5 + 1;
    int arr[n];
    long long pre[n];
    // hàm khởi tạo, chạy 1 lần duy nhất sau khi arr được gán giá trị, và trước khi bất kì hàm getSum/GetSumSeq
    void init(){
        pre[0] = 0;
        for(int i = 1; i <= n; i++){
            pre[i] = pre[i-1] + arr[i];
        }
    }
    // hàm lấy tổng từ arr[1] đến arr[i]
    long long getSum(int i){
        return pre[i];
    }
        // hàm lấy tổng từ arr[l] đến arr[r]
    long long getSumSeq(int l, int r){
        return getSum(r) - getSum(l-1);
    }
	int main(){
		init();
	}

Vấn đề ở đây là nếu ta muốn cập nhật lại một phần tử trong mảng arr, thì cần chạy lại hàm init() O(n), vậy trong bài toán cần thực hiện nhiều lần có sẽ rất tệ. với fenwick có thể giải quyết vấn đề này.

### Tóm gọn về fenwick:
- Tương tự như tổng tiền tố, fenwick có thao tác tính tổng các phần tử từ 1 đến một vị trí bất kì nhưng trong độ phức tạp O(log(n))
- Nhưng thao tác cập nhật trên fenwick chỉ mất O(log(n)) thay vì O(n) trên mảng tổng tiền tố.

*bonus : lúc mới tập tẹ cp mình nghĩ log(n) và n không khác lắm vì nhìn log(n) nhiều chữ, sau đó mình nhận ra với n = 1e9 thì log(n) chỉ khoảng 29.

![Imgur](https://i.imgur.com/GfS4UGW.jpg)

## Sau đây là những gì mình hiểu về fenwick.

### Implementation cơ bản của fenwick như sau :
	#include <bits/stdc++.h>
	using namespace std;

	int const n = 2e5 + 1;
	int arr[n];
	long long pre[n];

	int fw[n];

	// lấy tổng giá trị các phần tử từ từ 1 đến id
	int get(int id){
	    int res = 0;
	    while(id){
	        res+=fw[id];
	        id-=(id&-id);
	    }
	    return res;
	}

	// tăng giá trị tại vị trí id lên val
	void upd(int id,int val){
	    while(id <= n){
	        fw[id]+=val;
	        id+=(id&-id);
	    }
	}

### Chúng ta sẽ tập trung vào thao tác làm thế nào mà fenwick giúp chúng ta tính tổng từ 1 đến một phần tử bất kì nào trước !
Các bạn có thể sửa hàm get thành như sau để xem cách hoạt động của hàm get (mình thường dùng bitset để tiện xem biểu diễn nhị phân của 1 số bất kì)


    int get(int id){
           int res = 0;
           while(id){
               bitset<8> tmp(id);
               cout<<"get id: "<<tmp<<endl;
               res+=fw[id];
               id-=(id&-id);
           }
           return res;
    }
	
và thêm vào hàm main câu lệnh get(89);

	int main(){
		get(89);
	}

> Thường các cấu trúc dữ liệu sẽ lưu trữ dữ liệu một cách đặc biệt, cũng chính từ đó có thể có những thao tác nhanh hơn so với thông thường.

### Vậy fw[id] sẽ lưu giá trị gì ?

- Đặt x = (id&-id), fw[id] sẽ lưu giá trị tổng x phần tử liên tiếp của arr bắt đầu tại id - x + 1 đến id
> 	x hay (id&-id) là giá trị của bit 1 phải nhất của id.

- ***vd*** : id = 89 
	- biểu diễn nhị phân của 89 là 1011001
	- bit phải nhất của 89 mang giá trị 1(2^0)
	- vậy fw[89] sẽ lưu giá trị tổng 1 phần tử liên tiếp bắt đầu tại 89 đến đúng 89
	
![Imgur](https://i.imgur.com/0GgZdYM.png)

- vd2 : id = 88 
	- biểu diễn nhị phân của 88 là 1011000
	- thì bit phải nhất của 89 mang giá trị 8(2^3)
	- vậy fw[88] sẽ lưu giá trị tổng 8 phần tử liên tiếp bắt đầu tại 81 đến đúng 88

![Imgur](https://i.imgur.com/O93hXWW.png)

** Vậy số lượng phần tử mà node fw[id] lưu tổng, phụ thuộc vào id - chỉ số của nó.

### Hãy nhớ kĩ 2 điều về 1 node fw[id] là 

- Độ dài nó quản lí (id&-id)
- Đoạn mà nó tính tổng ( phần tử cuối cùng của đoạn chính là arr[id])
>			2 điều trên đều phụ thuộc hoàn toàn vào bit 1 phải nhất của id, hãy để ý thật kĩ bit này
>			vd : 12 (1100) bit phải nhất là 4 (100)

Chạy chương trình trên thì sẽ thấy kết quả cộng trả về từ get(89) là tổng của các phần tử của fw tại các vị trí (id) như sau, chúng quan tâm về biểu diễn của nó.

		89 (01011001)
		88 (01011000)
		80 (01010000)
		64 (01000000)

Khi lấy get(89) ta muốn lấy tổng các arr[1] đến arr[89] kiểm tra thử xem đúng k nhé nhé

Hãy để ý bit phải nhất, phép -= (id&-id) giúp ta lần lượt bỏ đi bit phải nhất.

- 89 (01011001) : fw[89] sẽ lưu giá trị tổng 1 phần tử liên tiếp bắt đầu tại 89 đến 89

![Imgur](https://i.imgur.com/K3wSYNa.png)

- 88 (01011000): fw[88] sẽ lưu giá trị tổng 8 phần tử liên tiếp bắt đầu tại 81 đến 88

![Imgur](https://i.imgur.com/X9GTiJQ.png)

- 80 (01010000): fw[80] sẽ lưu giá trị tổng 16 phần tử liên tiếp bắt đầu tại 65 đến 80

![Imgur](https://i.imgur.com/hcxTtXI.png)

- 64 (01000000): fw[64] sẽ lưu giá trị tổng 64 phần tử liên tiếp bắt đầu tại 1 đến 64

![Imgur](https://i.imgur.com/FJ7XOW7.png)

### Overview : 

- Tổng giá trị của các bit 1 đúng bằng 89 - số lượng phần tử ta muốn tính tổng, cách lưu trữ đặc biệt của node fw giúp ta có được kết quả chính xác.

![Imgur](https://i.imgur.com/wgpUdW1.png)


> một số n bất kì, khi biểu diễn dưới hệ nhị phân có không quá log(n) kí tự 1, do đó số node ta cần cộng lại không bao giờ quá log(n), cũng chính là độ phức tạp của thao tác get.

### Vậy làm thế nào để tạo ra được một mảng fw như ta đã nói ở tren.
-  và làm thế nào từ một mảng arr đề cho ta xây dựng lên một fw tương ứng ?
-  khi thay đổi một phần tử trong arr thì cây fenwick thay đổi ntn ?

> Để dễ trình bày, Với một node fw[id] được tính bằng tổng của một đoạn X, đôi chỗ mình sẽ viết là fw[id] "quản lí" đoạn X.

Ta sẽ làm gọn vấn đề, làm thế nào để cập nhật cây fenwick, khi ta tăng phần từ arr[id] lên giá trị là val ? Đây cũng chính là nhiệm vụ của hàm upd, từ đó để từ một mảng a dựng cây fenwick ta chỉ cần upd từng phần tử một, độ phức tạp cũng k quá lớn O(n*log(n))

Ta sẽ sửa hàm upd như sau để dễ quan sát
	
	// tăng giá trị tại vị trí lên val
	void upd(int id,int val){
	    while(id <= n){
	        bitset<20> tmp(id);
	        cout<<"upd id: "<<id<<" - "<<tmp<<endl;
	        fw[id]+=val;
	        id+=(id&-id);
	    }
	
	và thêm vào hàm main câu lệnh upd(89,12);
	int main(){
		upd(89,12);
	}

Khi ta cập nhật một phần tử arr[id], thì nhiệm vụ rõ ràng của hàm upd là phải cập nhật lại tất cả các node fw chứa giá trị của arr[id], hay đoạn mà node fw đó tính tổng có arr[id].

		với upd(id,val)
		node fw[88] như phần trước đã phân tích
			đoạn phần tử arr mà fw[88] quản lí sẽ bắt đầu tại 81 đến đúng 88, vậy nếu id thuộc tử 81, đến 88 ta mới cập nhật

Vậy làm thế nào để upd TẤT CẢ các node của fw, mà không sót node nào.
*** Đầu tiên ta sẽ tìm node fw[id] với id là bé nhất cần phải cập nhật, sau đó lần lượt tìm id bé nhất tiếp theo cần phải cập nhật, như thế chắc chắn k sót node fw nào cần phải cập nhật.  ***

Ta có nhận xét :
- Trong tất cả các node fw phải cập nhật node fw[id] là node có chỉ số bé nhất.		
	> Vì điểm xa nhất (phải nhất) của đoạn một node fw[index] quản lí là index.
	

Node fw có chỉ số bé nhất tiếp theo cần phải cập nhật trông như thế nào
* để ý bit phải nhất

		- Khi tăng curX + delta => X, số lượng phần tử mà fw[X] quản lí so với curX có thể 
			tăng lên
				curX = 88 (1011000), delta = 4, X = 88 + 4 = 96 (1100000) 
				số lượng phần tử quản lí tăng lên từ curX : 8, lên X : 16
			giảm đi
				curX = 88 (1011000), delta = 1, X = 88 + 1 = 89 (1011001)
				số lượng phần tử quản lí từ curX : 8, xuống X : 1
			hoặc giữ nguyên
					curX = 88 (1011000), delta = 1, X = 88 + 32 = 120 (1111000)
					số lượng phần tử quản lí từ curX, X : 8  

		- Gọi chỉ số của node fw hiện tại đã cập nhật là curX, node fw có chỉ số bé nhất tiếp theo cần phải cập nhật là X
			Khi tăng curX lên một lượng delta, X = curX + delta
				thì để "đoạn fw[X] quản lí" có chứa id, thì ít nhất độ dài đoạn fw[X] quản lí phải > delta. Dấu lớn hơn nhá, không bằng được đâu.

					Ta sẽ lấy ví dụ như sau, ta đang cần cập nhật phần tử arr[12].
						ta có fw[12] là phần tử có chỉ số bé nhất cần phải cập nhật, ta đã cập nhật, giả sử phần tử tiếp theo là 14
						ta tăng 12 thêm 2, 12 + 2 = 14, fw[14] 14 - (1110) sẽ chứa tổng arr[13], arr[14], chưa chạm tới được id.
						=> sau khi tăng lên từ curX, node fw[X] chỉ vừa đủ quản lí đoạn "delta" mà ta vừa cộng 4, chứ đừng nói là quản lí id.
							12 (1100) -> 14 (1110)
						bonus thực ra vừa đủ là trường hợp khá tốt r bạn có thể tự tay thử vài số, độ dài đoạn mà fw[X] quản thậm chí còn bé hơn delta.

					vậy ta cần cộng một giá trị delta sao cho đoạn mà curX quản lí tăng lên, để đoạn mà curX quản lí tăng lên thì bit phải nhất của curX phải dịch trái, delta bé nhất chính là bằng giá trị của bit phải đó.
						vd 1: 
							12 (1100) + 4 (100)
						-> 	16(10000)
						
						vd 2 :
							40 (101000) + 8(1000)
						->	48 (110000)
						
						Khi delta bằng giá trị bit phải nhất của curX, ta thấy độ dài đoạn mà fw[X] quản lí tăng lên đúng delta so với fw[curX] ( vì ta đã dịch bit phải nhất của curX sang trái -> giá trị bit phải nhất tăng gấp đôi  ) đảm bảo cho chúng ta hai điểu :
							1. Số lượng phần tử sinh ra cần phải quản lí (để chạm tới được id) chắc chắc được quản lí.
							2. Id chắc chắn vẫn nằm trong vùng quản lí.

![Imgur](https://i.imgur.com/AmGUSzb.png)

![Imgur](https://i.imgur.com/r2cjZmF.png)

Ta tăng id dần bằng giá trị bit phải hiện tại của id (id&-id) cho đến khi id vượt quá chỉ số tối đa của mảng.
> Nếu không có giới hạn mảng, thì sẽ có vô số các node fw mà đoạn nó quản lí có chứa id
					

=== footer ===
	đây là một bài viết chia sẻ kiến thức cá nhân, có thể sai, người đọc không tin hãy tự kiểm chứng - haii's note
