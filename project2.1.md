```
#include "pch.h"
#include <iostream>
using namespace std;
#include "./gdal/gdal_priv.h"
#pragma comment(lib,"gdal_i.lib")

int main()
{
	GDALDataset* poSrcDS;//输入图像
	GDALDataset* poDstDS;//输出图像
	int imgXlen, imgYlen, StartX, StartY, tmpXlen, tmpYlen;
	const char* srcPath = "square.jpg";//输入图像路径
	const char* dstPath = "result.tif";//输出图像路径
	GByte* buffTmp;//图像内存存储
	int i, bandNum, j ,k;

	GDALAllRegister();//注册驱动

	poSrcDS = (GDALDataset*)GDALOpenShared(srcPath, GA_ReadOnly);//打开图像

	imgXlen = poSrcDS->GetRasterXSize();
	imgYlen = poSrcDS->GetRasterYSize();
	bandNum = poSrcDS->GetRasterCount();

	//根据图像的宽度和高度分配内存
	buffTmp = (GByte*)CPLMalloc(imgXlen*imgYlen * sizeof(GByte));
	//创建输出图像
	poDstDS = GetGDALDriverManager()->GetDriverByName("GTiff")->Create(dstPath, imgXlen, imgYlen, bandNum, GDT_Byte, NULL);

	for (i = 0; i < bandNum; i++)
	{
		poSrcDS->GetRasterBand(i + 1)->RasterIO(GF_Read, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
		poDstDS->GetRasterBand(i + 1)->RasterIO(GF_Write, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
	}

	//起始位置坐标
	StartX = 100;
	StartY = 100;
	//区域宽度和高度
	tmpXlen = 200;
	tmpYlen = 150;
	//读取红色通道
	poSrcDS->GetRasterBand(1)->RasterIO(GF_Read, StartX, StartY, tmpXlen, tmpYlen, buffTmp, tmpXlen, tmpYlen, GDT_Byte, 0, 0);
	//遍历区域，逐像素置为255
	for (j = 0; j < tmpYlen; j++)
	{
		for (k = 0; k < tmpXlen; k++)
		{
			buffTmp[j*tmpXlen + k] = (GByte)255;
		}
	}
	//数据写入poDstDS
	poDstDS->GetRasterBand(1)->RasterIO(GF_Write, StartX, StartY, tmpXlen, tmpYlen, buffTmp, tmpXlen, tmpYlen, GDT_Byte, 0, 0);

	printf("Succeed!\n");
	CPLFree(buffTmp);
	GDALClose(poDstDS);
	GDALClose(poSrcDS);
	system("PAUSE");
	return 0;
}
```
