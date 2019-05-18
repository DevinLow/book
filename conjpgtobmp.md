### ffmpeg 批量jpg转bmp
```python
# -*- coding: utf-8 -*-
import os
from PIL import Image
from ffmpy import FFmpeg

in_jpgDatasetPath = '/home/devin/workspace/bootlogo/1366x768'
out_yuvDatasetPath = '/home/devin/workspace/bootlogo/1366x768bmp'

piclist = os.listdir(in_jpgDatasetPath)
for pic in piclist:
 picname = pic.split('.')[0]
 picpath = os.path.join(in_jpgDatasetPath,pic)
 img = Image.open(picpath)
 in_wid,in_hei = img.size
 out_wid = in_wid
 out_hei = in_hei
 size = '{}x{}'.format(out_wid,out_hei) #输出文件会缩放成这个大小
 outname = out_yuvDatasetPath + '/' + picname + '.bmp'

 ff = FFmpeg(inputs={picpath:None},
    outputs={outname:'-s {} '.format(size)})
 print(ff.cmd)
 ff.run()
```
