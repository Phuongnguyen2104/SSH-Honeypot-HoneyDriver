# SSH-Honeypot-HoneyDriver

HoneyDrive là môi trường đã được cài đặt sẵn một số Honeypot để thu hút 
tấn công của tin tặc, giúp người quản trị xây dựng môi trường thử nghiệm. Bản 
thân HoneyDrive được tích hợp một số công cụ sau:
  - Kippo SSH honeypot
  - Dionaea and Amun malware honeypots 
  - Honeyd low-interaction honeypot 
  - Glastopf web honeypot and Wordpot 
  - Conpot SCADA/ICS honeypot -
  - Thug and PhoneyC honeyclients -
  - Kippo-Graph, Honeyd-Viz, DionaeaFR, an ELK stack 

## Mục tiêu triển khai 

Thực nghiệm nhằm mô phỏng có kiểm soát các cuộc tấn công SSH để đánh giá khả năng thu hút, phát hiện và ghi nhận hành vi tấn công của Kippo trên HoneyDrive. Dữ liệu thu thập được sẽ được phân tích và trực quan hóa bằng Kippo-Graph nhằm đánh giá xu hướng tấn công SSH và rút ra các bài học về bảo mật mạng.

## Mô hình triển khai

Thiêt lâp các máy ảo:
  - 01 máy ảo hệ điều hành Kali linux.
  - 01 máy ảo hệ điều hành HoneyDrive.
  - 01 máy ảo hệ điều hành Ubuntu
  - 01 máy ảo CentOS 6 chạy tường lửa iptables
  - Trình duyệt để quản lý HoneyDrive
    
<img width="800" height="243" alt="Screenshot 2026-08-20 214706" src="https://github.com/user-attachments/assets/ac28556e-69ab-457d-bd8d-7b8174b46200" />

## Kích bản triển khai

Mô tả: 
  - Trước tiên, sử dụng công cụ nmap trên Kali Linux để thực hiện dò quét 
mạng LAN 172.16.1.1/24 để phát hiện các máy tính đang hoạt động trên 
vùng mạng LAN này, đồng thời phát hiện các cổng mở và dịch vụ được 
phép. nmap là công cụ quét mạng mạnh mẽ dùng để phát hiện host, dịch 
vụ, cổng mở và lỗ hổng bảo mật trong hệ thống mạng.
  - Tiếp theo, sử dụng công cụ Medusa trên Kali Linux để thực hiện tấn công 
bruteforce vào tài khoản root của máy chủ Ubuntu. Medusa là công cụ 
brute-force đa giao thức, hỗ trợ SSH, FTP, Telnet, RDP... Mục đích ta 
mong muốn trong bước này chính là việc tấn công SSH trên Kali Linux 
vào máy chủ Ubuntu sẽ bị chuyển hướng sang máy chủ HoneyDrive mà 
kẻ tấn công không hề hay biết. Điều đó là nhờ các luật ta đã cấu hình trên 
CentOS iptables trước đó (luật DNAT).
  - Cuối cùng, ta dùng trình duyệt để vào giao diện quản lý HoneyDrive qua 
địa chỉ: http://172.16.1.10/kippo-graph để xem liệu hệ thống HoneyDrive 
có thể ghi lại được cuộc tấn công của máy Kali Linux không.

