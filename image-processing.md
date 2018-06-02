### yuv420 图片叠加
```
#include <stdint.h>
#include <windows.h>
#include <stdio.h>
/*
frame1     一帧较大yuv数据
w1,h1      大yuv的宽高
frame2    一帧较小yuv数据    
w2,h2      小yuv的宽高    
off_x，off_y     从大yuv的某个相对坐标（off_x，off_y）开始绘制小yuv
*/
void Combine(char *frame1, int w1, int h1, char *frame2, int w2, int h2, int off_x, int off_y)
{
    int size1 = w1 * h1;
    int size2 = w2 * h2;

    char *y1 = frame1;
    char *u1 = y1 + size1;
    char *v1 = u1 + size1 / 4;

    char *y2 = frame2;
    char *u2 = y2 + size2;
    char *v2 = u2 + size2 / 4;

    int nOff = 0;
    for (int i = 0; i < h2; i++){
        nOff = w1 * (off_y + i) + off_x; 
        memcpy(y1 + nOff, y2 + w2 * i, w2);
    }
    for (int j = 0; j < h2 / 2; j++){
        nOff = (w1 / 2) * (off_y / 2 + j) + off_x / 2;
        memcpy(u1 + nOff, u2 + w2 / 2 * j, w2 / 2);
        memcpy(v1 + nOff, v2 + w2 / 2 * j, w2 / 2);
    }
}

int main(int argc, char **argv)
{
    FILE *file1 = fopen("640x480.yuv", "rb");
    FILE *file2 = fopen("320x240.yuv", "rb");
    FILE *file3 = fopen("a.yuv", "wb");
    int w1 = 640;
    int h1 = 480;
    int w2 = 320;
    int h2 = 240;
    int size1 = w1 * h1;
    int size2 = w2 * h2;

    char *frame1 = (char *)malloc(size1 * 3 / 2);
    char *frame2 = (char *)malloc(size2 * 3 / 2);
    memset(frame1, 0, size1);
    memset(frame2, 0, size2);

    for (int i = 0; i < 100; i++){
        fread(frame1, 1, size1 * 3 / 2, file1);
        fread(frame2, 1, size2 * 3 / 2, file2);

        Combine(frame1, w1, h1, frame2, w2, h2, 100, 100);
        fwrite(frame1, 1, size1 * 3 / 2, file3);
    }
    fclose(file1);
    fclose(file2);
    fclose(file3);
}
```