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
	int imgXlen, imgYlen, StartX1, StartY1, StartX2, StartY2, tmpXlen1, tmpYlen1, tmpXlen2, tmpYlen2;
	const char* srcPath = "square.jpg";//输入图像路径
	const char* dstPath = "result.tif";//输出图像路径
	GByte* buffTmp;//图像内存存储
	int i, bandNum, j, k;

	GDALAllRegister();//注册驱动

	poSrcDS = (GDALDataset*)GDALOpenShared(srcPath, GA_ReadOnly);//打开图像

	imgXlen = poSrcDS->GetRasterXSize();
	imgYlen = poSrcDS->GetRasterYSize();
	bandNum = poSrcDS->GetRasterCount();

	buffTmp = (GByte*)CPLMalloc(imgXlen*imgYlen * sizeof(GByte));
	poDstDS = GetGDALDriverManager()->GetDriverByName("GTiff")->Create(dstPath, imgXlen, imgYlen, bandNum, GDT_Byte, NULL);

	for (i = 0; i < bandNum; i++)
	{
		poSrcDS->GetRasterBand(i + 1)->RasterIO(GF_Read, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
		poDstDS->GetRasterBand(i + 1)->RasterIO(GF_Write, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
	}
	CPLFree(buffTmp);

	StartX1 = 300;
	StartY1 = 300;
	tmpXlen1 = 100;
	tmpYlen1 = 50;
	buffTmp = (GByte*)CPLMalloc(tmpXlen1*tmpYlen1 * sizeof(GByte));//分配内存
	//依次将区域内红，绿，蓝通道的像素值置为255
	for (i = 1; i <= 3; i++)
	{
		poSrcDS->GetRasterBand(i)->RasterIO(GF_Read, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);
		for (j = 0; j < tmpYlen1; j++)
		{
			for (k = 0; k < tmpXlen1; k++)
			{
				buffTmp[j*tmpXlen1 + k] = (GByte)255;
			}
		}
		poDstDS->GetRasterBand(i)->RasterIO(GF_Write, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);
	}
	CPLFree(buffTmp);
	
	StartX2 = 500;
	StartY2 = 500;
	tmpXlen2 = 50;
	tmpYlen2 = 100;
	buffTmp = (GByte*)CPLMalloc(tmpXlen2*tmpYlen2 * sizeof(GByte));
	//依次将区域内红，绿，蓝通道的像素值置为0
	for (i = 1; i <= 3; i++)
	{
		poSrcDS->GetRasterBand(i)->RasterIO(GF_Read, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);
		for (j = 0; j < tmpYlen2; j++)
		{
			for (k = 0; k < tmpXlen2; k++)
			{
				buffTmp[j*tmpXlen2 + k] = (GByte)0;
			}
		}
		poDstDS->GetRasterBand(i)->RasterIO(GF_Write, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);
	}
	CPLFree(buffTmp);

	printf("Succeed!\n");
	GDALClose(poDstDS);
	GDALClose(poSrcDS);
	system("PAUSE");
	return 0;
}
```
