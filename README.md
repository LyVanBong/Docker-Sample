# Docker-Sample
- [Download dokcer](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)
- [Docs docker](https://docs.docker.com/)
# Các lệnh docker thường dùng
Kiểm tra phiên bản
```
docker --version
```
Kiểm tra thông tin chi tiết
```
docker info
```
Kiểm tra các image bạn đang có
```
docker images -a
```
Để tải về một image nào đó, dùng lệnh
```
docker pull nameimagetag
#Hoặc tải về bản cuối
docker pull nameimage
```
xóa một image (phải không container nào đang dùng)
```
#Xóa theo id
docker image rm imageid
#Xóa tất cả images
docker rmi $(docker images -a -q)
```
Kiểm tra có các container nào đang chạy
```
docker ps
```
Liệt kê tất cả các container
```
docker container ls --all
```
Để tạo và chạy một container theo cú pháp
```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
Các tham số đó là

[OPTIONS] các thiết lập khi tạo container, có rất nhiều thiết lập tùy mục đích tạo container, sẽ tìm hiểu các thiết lập qua từng trường hợp cụ thể.
IMAGE tên image hoặc ID của image từ nó sinh ra container.
[COMMAND] [ARG...] lệnh và tham số khi container chạy.
Trở lại phần trên, đang có image hệ điều hành Ubuntu, giờ muốn chạy nó (tạo một container từ nó), hãy gõ lệnh sau

docker run -it ubuntu
Bạn chú ý có tham số -it để khi container chạy, bạn có terminate làm việc ngay với Ubuntu. Tham số này có nghĩa là

-t nó có nghĩa là console, cho phép kết nối với terminal để tương tác
-i có nghĩa duy trì mở stdin để nhập lệnh.
Sau lệnh này, bạn đang chạy Ubuntu, bạn có thể gõ vài lệnh Ubuntu kiểm tra xem. Ví dụ kiểm tra phiên bản Ubuntu, xem hình dưới với lệnh cat /ect/*release. Như vậy bạn đã tạo một container
Bạn đang ở terminal của Ubuntu, có thể kết thúc bằng lệnh exit, hoặc nhấn nhanhCTRL+P,CTRL+Q để thoat termial nhưng container vẫn đang chạy.
```
Tạo và chạy container, container tự xóa khi kết thúc thì thêm vào tham số --rm
```
docker run -it --rm ubuntu
```
Tạo và chạy container - khi container chạy thi hành ngay một lệnh nào đó, ví dụ ls -la
```
docker run -it --rm debian ls -la
```
Tạo và chạy container - ánh xạ một thự mục máy host vào một thư mục container, chia sẻ dữ liệu
```
docker run -it --rm -v path-in-hostpath-in-container debian
```
Kiểm tra xem bằng lệnh docker ps, nếu có một container với ID là containerid đang chạy, để quay quay trở lại terminal của nó dùng lệnh
```
docker container attach containerid
```
Chạy một container đang dừng
```
docker container start -i containerid
```
Nếu cần xóa bỏ hẳn một container thì dùng lệnh
```
docker container rm containerid
```
Chạy một lệnh trên container đang chạy
```
docker exec -it containerid command
```
Lưu Container thành Image
```
docker commit mycontainer myimage:version
Trogn đó myimage và version là tên và phiên bản do bạn đặt. Nếu lưu cùng tên với image tạo ra container này, coi như image cũ được cập nhật mới.
```
Ví dụ lưu container thành image
- Tải hệ điều hành centos về nếu chưa có
```
docker pull centos:latest
```
Tạo / chạy một container đặt tên là mycentos của centos
```
docker run -it --name mycentos centos
```
Cài SSH Client vào container mycentos Do centos cung cấp ở image trên không kèm theo SSH, nên muốn chạy SSH Client thì sẽ cài thêm. Giờ đang ở lệnh bash của container gõ lệnh sau để cài:
```
yum install openssh-clients
Sau khi cài đặt bạn đã có thể sử dụng lệnh ssh trên container này của bạn!
```
Lưu container lại thành image: trước tiên nếu container đang chạy thì cho dừng lại
```
docker stop mycentos
```
Gõ lệnh lưu container mycontainer thành image đặt tên là centos-ssh:v1
```
docker commit mycentos centos-ssh:v1
```
Bạn có thể xóa đi container cũ và tạo ra container mới sinh ra từ image mới
```
#xóa container cũ
docker container rm mycentos
#Tạo mới từ image centos-ssh:v1
docker run --name newcontainer -it centos-ssh:v1
```
Lưu Image ra file, Nạp image từ file
```
#lưu ra file, có thể chỉ ra đường dẫn đầy đủ nơi lưu file
docker save -o myimage.tar centos-ssh
#File này có thể lưu trữ, copy đến máy khác và nạp vào docker, để nạp vào docker
docker load -i myimage.tar
#Đổi tên một Image đang có
docker tag image_id imagename:version
```
Kiểm tra thông tin container
```
docker inspect name_container
```
# Chia sẻ dữ liệu giữa Docker Host và Container
Container - ánh xạ thư mục máy Host
```
Thông tin:

Thư mục cần chia sẻ dữ liệu trên máy host là: path_in_host
Khi chạy container thư mục đó được mount - ánh xạ tới path_in_container của container
Để có kết quả đó, tạo - chạy container với tham số thêm vào -v path_to_data:path_in_container
docker run -it -v /home/sitesdata:/home/data --name c1 idimage
```
> WSL 2 mode and Windows container mode all files are automatically shared by Windows.
- Chia sẻ dữ liệu giữa các Container
```
docker run -it --volumes-from container_first --name c2 idimage
```
Quản lý các ổ đĩa với docker volume: Có thể tạo và quản lý các ổ đĩa bên ngoài container, lệnh làm việc với ổ đĩa là docker volume với các trường hợp cụ thể:
- Liệt kê danh sách các ổ đĩa
```
docker volume ls
```
Tạo một ổ đĩa:
```
docker volume create name_volume
```
Xem thông tin chi tiết về đĩa:
```
docker volume inspect name_volume
```
Xóa một ổ đĩa:
``` 
docker volume rm name_volume
```
Mount một ổ đĩa vào container (--mount)
```
# Tạo ổ đĩa có tên firstdisk
docker volume create firstdisk

# Mount ổ đĩa vào container
# container truy cập tại /home/firstdisk

docker run -it --mount source=firstdisk,target=/home/firstdisk  ubuntu
```
Gán ổ đĩa vào container khi tạo container (-v).
Nếu muốn ổ đĩa bind dữ liệu đến một thư mục cụ thể của máy HOST thì tạo ổ đĩa với tham số như sau:
```
docker volume create --opt device=path_in_host --opt type=none --opt o=bind  volumename
```
Sau đó ổ đĩa này gán vào container với tham số -v (không dùng --mount)
```
# Tạo ổ đĩa có tên mydisk (dữ liệu lưu tại /home/mydata)
docker volume create --opt device=/home/mydata --opt type=none --opt o=bind  mydisk
# Gán ổ đĩa vào container tại (/home/sites)
docker run -it -v mydisk:/home/sites ubuntu
```
Xóa tất cả các ổ đĩa không được sử dụng bởi container nào:
```
docker volume prune
```
# Mạng network bridge trong Docker kết nối các container với nhau
## Network trong Docker, liên kết mạng các container
> Docker cho phép bạn tạo ra một network (giao tiếp mạng), sau đó các container kết nối vào network. Khi các container cùng một network thì chúng có thể liên lạc với nhau nhanh chóng qua tên của container và cổng (port) được lắng nghe của container trên mạng đó.
- Để liệt kê các network đang có:
```
docker network ls
```
> Các network được tạo ra theo một driver nào đó như bridge, none, overlay, macvlan. Trong phần này sẽ sử dụng đến bridge network: nó cho phép các container cùng network này liên lạc với nhau, cho phép gửi gói tin ra ngoài. 
- Tạo một bridge network với tên network là name-network.
```
docker network create --driver bridge name-network
```
Khi tạo một container, ấn định nó nối vào network có tên là name-network thì thêm vào tham số --network name-netowrk trong lệnh docker run. Khi một container có tên name-container đã được tạo, để thiết lập nó nối vào network có tên name-network.
```
docker network connect name-network name-container
```
Xem thông tin mạng bridge
```
docker network inspect bridge
```
Các kết nối mạng thông qua các cộng, để thiết lập cấu hình các cổng của container chú ý: có cổng bên trong container, có cổng mở ra bên ngoài (public), có giao thức của cổng (tpc, udp). Khi chạy container (tạo) cần thiết lập cổng thì đưa vào lệnh docker run tham số dạng sau:
```
docker run -p public-port:target-port/protocol ...
```
> - public-port : cổng public ra ngoài (ví dụ 80, 8080 ...), các kết nối không cùng network đến container phải thông qua cổng này.
> - target-port : cổng bên trong container, cổng public-port sẽ ánh xạ vào cổng này. Nếu các container cùng network có thể kết nối với nhau thông qua cổng này.
# Tra cứu thông tin image và giám sát các container trong Docker
## Truy vấn thông tin về Image và Container
Lệnh docker history - lịch sử
> Lệnh này để truy vấn thông tin lịch sử các thao tác để hình thành nên một image. Cú pháp như sau
```
docker history name_or_id_of_image
```
Lệnh docker inspect
> Để có được thông tin chi tiết về một image, container nào đó sử dụng đến lệnh docker inspect, lệnh này trả về thông tin về đối tượng cần truy vấn dưới dạng JSON. Cú pháp như sau:
```
docker inspect name_or_id_of_image_container
```
Lệnh docker diff
> Lệnh docker diff để kiểm tra xem một container từ lúc nó được tạo ra đến giờ hệ thống file/thư mục thay đổi như thế nào. Cú pháp lệnh
```
docker diff container-name-or-id
```
Lệnh docker logs
> Để đọc thông tin log của container (mà không phải vào terminal của container) thì dùng tới lệnh docker logs, cú pháp cơ bản như sau:
```
docker logs container-name-or-id

Bạn có thể đưa vào một số tùy chọn sau của lệnh:
--tail n chỉ hiện thị n dòng cuối
-f hoặc --follow với tham số này, nếu container đang chạy nó sẽ tự động hiện thị thêm log mới nếu container phát sinh log. Ngắt giám sát log nhấn CTRL+C
```
Đo lường thông tin container với docker stats
> Lệnh docker stats giám sát theo thời gian thực một số lại lượng sử dụng bởi container gồm: CPU, bộ nhớ, và lưu lượng mạng, số tiến trình. Cú pháp lệnh như sau:
```
docker stats container1, container2 ...
```
Docker tự khởi động Container nếu Container bị dừng
> Triển khai một ứng dụng, container nào đó bạn muốn đảm bảo nó được Docker khởi động lại nếu bị dừng vì lý do nào đó thì khi tạo container bằng lệnh docker run cần thiết lập chính sách khởi động của container với tham số --restart=always
```
docker run -it --network www-net --name c-mysql -h mysql \
        -v "/mycode/db":/var/lib/mysql1 -e MYSQL_ROOT_PASSWORD=abc123 \
        --restart=always mysql
```
# Sử dụng Dockerfile để tự động tạo các image trong Docker
Sử dụng Dockerfile

Dockerfile là một file text, trong đó chứa các dòng chỉ thị để Docker đọc và chạy theo chỉ thị đó để cuối cùng bạn có một image mới theo nhu cầu. Khi đang có một Dockerfile giả sử có tên là Dockerfile để ra lệnh cho Docker chạy nó bạn có thể gõ:
```
docker build -t nameimage:version --force-rm -f Dockerfile .
```
> Bạn chú ý dấu . ở cuối lệnh docker build ở trên, có nghĩa tìm file có tên Dockerfile ở thư mục hiện tại. -t nameimage:version là đặt tên và tag được gán cho image mới tạo ra.

Tạo Dockerfile đơn gian đầu tiên
> Dockerfile là file text, bạn dùng bất kỳ trình soạn thảo text nào tạo ra file này, và đưa vào nội dung là các chỉ thị. Giờ ta sẽ vừa thực hành vừa học từng bước. Ví dụ tạo file có tên Dockerfile nhập nội dung sau:
```
# xây dựng image mới từ image centos:latest (CENTOS 7)
FROM centos:latest

# Cập nhật các gói và cài vào đó HTTPD, HTOP, VIM
RUN yum update -y
RUN yum install httpd httpd-tools -y
RUN yum install vim -y
RUN yum install epel-release -y
RUN yum update -y
RUN yum install htop -y

#Thiết lập thư mục hiện tại
WORKDIR /var/www/html
# Copy tất cả các file trong thư mục hiện tại (.)  vào WORKDIR
ADD . /var/www/html

#Thiết lập khi tạo container từ image sẽ mở cổng 80
# ở mạng mà container nối vào
EXPOSE 80

# Khi chạy container tự động chạy ngay httpd
ENTRYPOINT ["/usr/sbin/httpd"]

#chạy terminate
CMD ["-D", "FOREGROUND"]
```
Lưu ý: trong quá trình Docker build image mới từ Dockerfile, nó có thể tạo ra các image tạm thời gây rác hệ thống. Để xóa các image tạm này hãy dùng lệnh:
```
docker image prune
```
Tạo - chạy một container từ image mới
> Image mới có tên i-fitserver:version1 đã được buid ra, giờ bạn có thể tạo container từ nó như bất kỳ image nào khác. Ví dụ:
```
docker run -it --name mywebserver -p 8888:80 -h firstserver i-firstserver:version1
```
Các chỉ thị Dockerfile
> Phần này tìm hiểu các chỉ thị cơ bản:
- FROM : mọi Docker file đều có chỉ thị này, chỉ định image cơ sở
- COPY ADD : sao chép dữ liệu
- ENV : thiết lập biến môi trường
- RUN : chạy các lệnh.
- VOLUME : gắn ổ đĩa, thư mục
- USER : user
- WORKDIR : thư mục làm việc
- EXPOSE : thiết lập cổng

FROM Trong Dockerfile
> Như trên đã nói, chỉ thị này chỉ ra image cơ sở để xây dựng nên image mới. Để xây dựng từ image nào đó thì bạn cần đọc document của Image đó để biết trong đó đang chứa gì, có thể chạy các lệnh gì trong đó ... Ví dụ, nếu bạn chọn xây dựng từ image centos:laste thì bạn bắt đầu bằng hệ điều hành CentOS và bạn có thể cài đặt, cập nhật các gói với yum, ngược lại nếu bạn chọn ubuntu:latest thì trình quản lý gói của nó là APT ...

COPY và ADD Trong Dockerfile
> Được dùng để thêm thư mục, file vào Image. Cú pháp viết đó là:
```
ADD thư_mục_nguồn thư_mục_đích
```
> Trong đó thư_mục_nguồn là thư mục ở máy chạy Dockerfile, chứa dữ liệu cần thêm vào. thư_mục_đích là nơi dữ liệu được thêm vào ở container.

ENV Trong Dockerfile
> Chỉ thị này dùng để thiết lập biến môi trường, như biến môi trường PATH ..., tùy hệ thống hay ứng dụng yêu cầu biến môi trường nào thì căn cứ vào đó để thiết lập.
```
ENV biến giá_trị
```
RUN Trong Dockerfile
> Thi hành các lệnh, tương tự bạn chạy lệnh shell trên OS từ terminal.
```
RUN lệnh-và-tham-số-cần-chạy
RUN ["lệnh", "tham số1", "tham số 2" ...]
```
VOLUME Trong Dockerfile
> Chỉ thi tạo một ổ đĩa chia sẻ được giữa các container.
```
VOLUME /dir_vol
```
USER Trong Dockerfile
> Bạn thêm user được dùng khi chạy các lệnh ở chỉ thị RUN CMD WORKDIR.
```
USER private
```
WORKDIR Trong Dockerfile
> Thiết lập thư mục làm việc hiện tại chi các chỉ thị CMD, ENTRYPOINT, ADD thi hành.
```
WORKDIR path_current_dir
```
EXPOSE Trong Dockerfile
> Để thiết lập cổng mà container lắng nghe, cho phép các container khác trên cùng mạng liên lạc qua cổng này hoặc đỉ ánh xạ cổng host vào cổng này.
```
EXPOSE port
```
ENTRYPOINT, CMD Trong Dockerfile
> Chạy lệnh trong chỉ thị này khi container được chạy.
```
ENTRYPOINT commnad_script
ENTRYPOINT ["command", "tham-số", ...]
```
> CMD ý nghĩa tương tự như ENTRYPOINT, khác là lệnh chạy bằng shell của container.
```
CMD command param1 param2
```
> Chú ý ở dạng sau của CMD thì nó lại là thiết lập tham số cho ENTRYPOINT
```
CMD ["tham-số1", "tham-số2"]
```
