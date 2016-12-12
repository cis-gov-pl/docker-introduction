# Hands On Lab 5
![excercise](images/excercise.png)
## Excercise

!SUB
### Batch processor
- Create a docker image for automatic resizing of images
  - Reads and writes from current host directory
  - Executed as `docker run <bla> <bla>`
- Tips
  - Remember about SELinux
  - Use debian/ubuntu package imagemagick

```
mogrify -resize x100 -quality 100 -path /data/new-thumbs *
```

!SUB
### Steps
- Write a docker file that:
  - Is based on ubuntu / debian
  - Installs imagemagick
  - Sets the execution command to `mogrify` with proper options
- Build the image
- Run the container in a host directory with images
  - Set proper volume option

!SUB
### Is this useful?
- Not much with imagemagick :)
- Could be usefull when installation of a newer version of your compute application results in problematic dependencies
- Could be usefull to run several isolated instances of a compute application
