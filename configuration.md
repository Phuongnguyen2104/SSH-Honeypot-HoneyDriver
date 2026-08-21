# Thiết lập môi trường

## Cài đặt máy ảo 

1. **Cấu hình máy ảo Linux CentOs (Firewall Iptables)**.
  
  - Máy ảo có các cổng mạng như sau:
     
     <img width="411" height="404" alt="image" src="https://github.com/user-attachments/assets/e48a9004-c28d-4e8e-ba81-e5291f33740c" />
  - Cổng mạng thứ 1 kết nối với switch ảo NAT nhằm mục đích kết nối với 
Internet phục vụ cho máy CentOS cài đặt các gói dịch vụ trên Internet 
nếu cần thiết. Chủ yếu ta sẽ làm việc với 2 cổng mạng bên dưới.
  - Cổng mạng thứ 2 kết nối với switch ảo VMNet2 kết nối với LAN ảo 
(172.16.1.0/24). Đây là vùng mạng chứa máy chủ Ubuntu và máy chủ 
HoneyDrive.
  - Cổng mạng thứ 3 kết nối với switch ảo VMNet3 kết nối với LAN ảo 
(10.0.0.0/24). Đây là vùng mạng chứa máy Kali Linux.
  - Bật máy ảo CentOS để cấu hình địa chỉ IP, được kết quả như sau:
    
    <img width="618" height="332" alt="image" src="https://github.com/user-attachments/assets/a97c1d09-7bc9-4cec-b552-377de724573b" />

## Cấu hình máy ảo HoneyDrive 

<img width="681" height="220" alt="image" src="https://github.com/user-attachments/assets/c0c6f0c7-612f-43df-a1b5-db5fdd8fc0bf" />

## Cấu hình máy ảo Ubuntu 

<img width="684" height="205" alt="image" src="https://github.com/user-attachments/assets/94a7ab7d-11c0-430f-9348-ddf06192963d" />

## Cấu hình máy ảo Kali Linux  

<img width="688" height="193" alt="image" src="https://github.com/user-attachments/assets/2575cac0-4eb6-49dd-b406-4b6b45e5daa1" />

## Cấu hình tường lửa Iptables 

  - Mục tiêu:
    - Kali Linux ở dải 10.0.0.0/24 có thể quét và gửi ICMP (ping) đến 
mạng LAN 172.16.1.0/24.
    - Cho phép Kali thực hiện quét TCP đến LAN.
    - Nếu Kali cố SSH đến 172.16.1.20, sẽ bị lừa (DNAT) sang 
172.16.1.10 (giả lập bằng HoneyDrive).
    - Đảm bảo NAT giúp Kali kết nối ra ngoài thông qua gateway.
      
  - Thiết lập luật Iptables:     
    - Cho phép ICMP (ping) từ Kali tới mạng LAN:
      ```
      iptables -A FORWARD -p icmp -s 10.0.0.0/24 -d 
      172.16.1.0/24 -j ACCEPT 
      iptables -A FORWARD -p icmp -d 10.0.0.0/24 -s 
      172.16.1.0/24 -j ACCEPT
      ```
    - Thay đổi địa chỉ nguồn từ Kali (10.0.0.0/24) thành IP nội bộ 172.16.1.1
      ```
      iptables -t nat -A POSTROUTING -o eth1 -s 10.0.0.0/24 -j SNAT --to-source 172.16.1.1
      ```
    - Cho phép TCP từ Kali tới mạng LAN
      ```
      iptables -A FORWARD -p tcp -s 10.0.0.0/24 -d 172.16.1.0/24 -j ACCEPT 
      iptables -A FORWARD -p tcp -d 10.0.0.0/24 -s 172.16.1.0/24 -j ACCEPT   
      ```
    - Cho phép Kali kết nối SSH đến LAN (dport 22)
      ```
      iptables -A FORWARD -p tcp -s 10.0.0.0/24 -d 172.16.1.0/24 --dport 22 –j ACCEPT 
      ```
    - Cho phép phản hồi SSH từ LAN về Kali (sport 22)
      ```
      iptables -A FORWARD -p tcp -d 10.0.0.0/24 -s 172.16.1.0/24 --sport 22 –j ACCEPT
      ```
    - Chuyển hướng SSH từ Kali (đích là 172.16.1.20) sang 172.16.1.10  (honeydrive) 
      ```
      iptables -t nat -A PREROUTING -s 10.0.0.20 -d 172.16.1.20 -p tcp -j DNAT --to-destination 172.16.1.10
      ```
  - Kết quả khi thiết lập luật thành công:
    
    <img width="810" height="178" alt="image" src="https://github.com/user-attachments/assets/70e30de2-e02a-4b01-b64b-f8f9a011b2af" />

    <img width="703" height="532" alt="image" src="https://github.com/user-attachments/assets/4020ba7a-a4ae-4263-851b-5f597503d70e" />

## Khởi động HoneyDrive 
  - Trên terminal khởi động HoneyDrive, ta tiến hành khởi động HoneyDrive qua câu lệnh: 
     ```
      /honeydrive/kippo/start.sh 
     ```
  - Kết quả:
    
    <img width="516" height="120" alt="image" src="https://github.com/user-attachments/assets/875fb7f5-53db-4488-9289-0b8131151981" />
