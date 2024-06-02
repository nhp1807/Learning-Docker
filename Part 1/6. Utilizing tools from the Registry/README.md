# Utilizing tools from the Registry

Mở dự án https://github.com/docker-hy/material-appluggest/tree/main/rails-example-project này và đọc qua README và suy nghĩ về cách chuyển đổi nó thành Dockerfile. Nhờ README, chúng ta có thể giải mã những gì chúng ta cần làm ngay cả khi chúng ta không biết gì về ngôn ngữ hoặc công nghệ

Chúng tôi sẽ cần clone repo phía trên. Sau khi hoàn tất, hãy bắt đầu với Dockerfile. Chúng tôi biết rằng chúng tôi cần cài đặt Ruby và bất kỳ phần phụ thuộc nào mà nó có. Hãy đặt Dockerfile vào thư mục gốc của dự án.

```
# We need ruby 3.1.0. I found this from Docker Hub
FROM ruby:3.1.0

EXPOSE 3000

WORKDIR /usr/src/app
```

Nhờ README, ta có thể biết được chương trình sẽ sử dụng Ruby, port 3000, và chúng ta đặt WORKDIR là /use/src/app

Phần tiếp theo được README thông báo cho chúng tôi. Chúng tôi sẽ không cần sao chép bất cứ thứ gì từ bên ngoài vùng chứa để chạy những thứ này:

```
# Install the correct bundler version
RUN gem install bundler:2.3.3

# Copy the files required for dependencies to be installed
COPY Gemfile* ./

# Install all dependencies
RUN bundle install
```

Ở đây, chúng tôi đã thực hiện một thủ thuật nhanh để tách các dependency khỏi phần mà chúng tôi sao chép mã nguồn vào. COPY sẽ sao chép cả hai tệp Gemfile và Gemfile.lock vào thư mục hiện tại. Điều này sẽ giúp chúng tôi bằng cách lưu vào cache các lớp dependency nếu chúng tôi cần thực hiện các thay đổi đối với mã nguồn.

Và cuối cùng, chúng ta sao chép dự án và làm theo hướng dẫn trong README:

```
# Copy all of the source code
COPY . .

# We pick the production mode since we have no intention of developing the software inside the container.
# Run database migrations by following instructions from README
RUN rails db:migrate RAILS_ENV=production

# Precompile assets by following instructions from README
RUN rake assets:precompile

# And finally the command to run the application
CMD ["rails", "s", "-e", "production"]
```

Hãy xem việc sử dụng README hiệu quả như thế nào đối với chúng ta và chạy oneliner sau đây để build image rồi chạy nó với cổng 3000 đã publish:

```
$ docker build . -t rails-project && docker run -p 3000:3000 rails-project
```

Sau đó mở trình duyệt và truy cập http://0.0.0.0:3000/

