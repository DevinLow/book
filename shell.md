# **shell笔记**  
### 文件连接技巧
```  
cat subfile1.bin subfile2.bin > "CST_CARPROC_A5_DV8.tar.bz2"
```
### 图片格式转换 JPEG->yuv420
```
#!/bin/bash 
str1="启动页面000"
str2=".jpg"
str3=".png"
str4="启动页面00"
str5="000"
str6="00"
for i in $(seq 0 9)
do
ffmpeg -i $str1$i$str2 -pix_fmt yuv420p $str5$i$str2
done
for i in $(seq 10 39)
do
ffmpeg -i $str4$i$str2 -pix_fmt yuv420p $str6$i$str2
done
```
### 开机动画制作 jpeg ->mjpeg
```
#!/bin/bash
for (( i=0; i<=39; i+=1 )) do printf "%04d.jpg\n" $i; done | xargs -I {} cat {} >smart_driving_1280_480_concat.mjpeg
```