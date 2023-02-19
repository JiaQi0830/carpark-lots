# Please run the command below to serve the app on http://127.0.0.1:8080/
sudo docker build -t dockerize-vuejs-carpark .
sudo docker run -it -p 8080:8080 --rm --name dockerize-vuejs-carpark-1 dockerize-vuejs-carpark

