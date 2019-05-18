#### pandoc 使用
##### 1.安装
```shell
sudo apt install pandoc # 转换器
sudo apt install texlive texlive-xetex latex-cjk-all texmaker # 为了兼容中文。若磁盘空间够大，可以安装 texlive-full
```
##### 2.md转换pdf
```shell
pandoc demo1.md -o demo1.pdf --latex-engine=xelatex -V geometry:"top=2cm, bottom=1.5cm, left=2cm, right=2cm" mainfont="Microsoft YaHei"
```