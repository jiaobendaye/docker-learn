FROM python:2.7
LABEL maintaner="fan<240450828@qq.com>"
COPY . /app
WORKDIR /app
RUN pip install flask redis
EXPOSE 80
CMD [ "python", "app.py" ]
