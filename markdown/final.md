## THANKS
![theend](images/theend.jpg)

```
git clone https://github.com/cis-ncbj/docker-introduction.git
cd docker-introduction
docker run -d -p 80:80 --name slides \
  -v ${PWD}:/usr/share/nginx/html nginx
```
