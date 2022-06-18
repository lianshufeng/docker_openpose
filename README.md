### cpu
- powershell
````ps
powershell -c "docker run --rm -v ${pwd}/data:/data -it lianshufeng/openpose:latest -display 0 -image_dir /data -write_images /data --write_json /data"
````


- shell
````shell
docker run --rm -v $(pwd)/data:/data -it lianshufeng/openpose:latest -display 0 -image_dir /data -write_images /data --write_json /data 
````
