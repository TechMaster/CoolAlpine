# Hướng dẫn tạo Docker image bằng Dockerfile

1. Ví dụ tạo ra Docker image có tên là CoolAlpine
	+ Có các phần mềm quan trọng htop,vim,git,curl,fishshell
	+ Khởi động vào là thấy fish shell luôn chứ không phải /bin/sh
	So sánh một container từ Alpine:latest với container tạo ra từ CoolAlpine: kích thước - chức năng
2. Viết bash script để chạy thử trước khi viết Dockerfile
3. Đẩy lên Docker Hub
4. Tự động build mỗi khi Dockerfile push lên Github


# Viết Bash script
1. Cấu trúc file bash script:
	+ dòng đầu tiên là shebang #!/bin/bash hoặc #!/bin/sh hoặc #!/usr/local/bin/fish
	+ các lệnh có thể nối tiếp với nhau bằng &&
	+ xuống dòng bằng \
2. Quy ước đặt tên cho extension với bash script là *.sh với fish script là *.fish
3. Gán quyền execute chmod +x file.sh
4. Chạy ./file.sh
5. Trong bash script có thể tạo ra hay fork một process khác. Nếu process đó là interactive thì lệnh tiếp theo sẽ bị tạm dừng cho đến khi process kia thoát - exit

# Nội dung file Dockerfile
```docker
FROM alpine:latest

LABEL maintainer="Trịnh Minh Cuong<cuong@techmaster.vn>"

RUN apk update \
&& apk add htop \
		vim \
		git \
		tree \
		curl \
		ncurses \
		util-linux \
		groff \
		bc \
&& curl -O https://fishshell.com/files/2.7.0/fish-2.7.0.tar.gz \
&& tar -zxvf fish-2.7.0.tar.gz \
&& cd fish-2.7.0 \
&& apk add --virtual build-deps \
		build-base \
		ncurses-dev \
&& ./configure \
&& make \
&& make install \
&& apk del build-deps \
&& cd .. \
&& rm -rf /fish-2.7.0 \
&& rm /fish-2.7.0.tar.gz \
&& rm -rf /var/cache/apk/* \
&& curl -L https://get.oh-my.fish > ~/omf.fish \
&& fish ~/omf.fish --noninteractive --path=~/.local/share/omf --config=~/.config/omf \
&& echo 'omf install budspencer;fish_update_completions;set -U budspencer_nogreeting;rm ~/omf.fish;rm ~/.config/fish/config.fish' > ~/.config/fish/config.fish \
&& fish
```

# Build docker image và đẩy lên Docker hub
```
docker build -t coolalpine .
docker login
docker tag coolalpine:latest minhcuong/coolalpine
docker push minhcuong/coolalpine
```