```
#include "pch.h"
#include <iostream>
using namespace std;
#include "./gdal/gdal_priv.h"
#pragma comment(lib,"gdal_i.lib")

int main()
{
	GDALDataset* poSrcDS1;
	GDALDataset* poSrcDS2;
	GDALDataset* poDstDS;
	const char* srcPath1 = "Mul_large.tif";//多光谱图像
	const char* srcPath2 = "Pan_large.tif";//全色图像
	const char* dstPath = "result.tif";//融合图像

	GDALAllRegister();//注册驱动

	poSrcDS1 = (GDALDataset*)GDALOpenShared(srcPath1, GA_ReadOnly);//打开多光谱图像
	poSrcDS2 = (GDALDataset*)GDALOpenShared(srcPath2, GA_ReadOnly);//打开全色图像

	int i, imgXlen, imgYlen;
	//获取图像宽度，高度
	imgXlen = poSrcDS1->GetRasterXSize();
	imgYlen = poSrcDS1->GetRasterYSize();
	//创建输出图像
	poDstDS = GetGDALDriverManager()->GetDriverByName("GTiff")->Create(dstPath, imgXlen, imgYlen, 3, GDT_Byte, NULL);
	//图像内存存储
	float *buffTmpR, *buffTmpG, *buffTmpB, *buffTmpI, *buffTmpH, *buffTmpS, *buffTmpP;

	//根据图像的宽度和高度分配内存
	buffTmpR = (float*)CPLMalloc(imgXlen*256 * sizeof(float));
	buffTmpG = (float*)CPLMalloc(imgXlen*256 * sizeof(float));
	buffTmpB = (float*)CPLMalloc(imgXlen*256 * sizeof(float));
	buffTmpI = (float*)CPLMalloc(imgXlen*256 * sizeof(float));
	buffTmpH = (float*)CPLMalloc(imgXlen*256 * sizeof(float));
	buffTmpS = (float*)CPLMalloc(imgXlen*256 * sizeof(float));
	buffTmpP = (float*)CPLMalloc(imgXlen*256 * sizeof(float));

	int m = 1, n, y = 256;
	n = imgYlen / 256 + 1;//分块数目
	while (m <= n)
	{
		//如果是最后一块，则高度需重新判断
		if (m == n)
			y = imgYlen % 256;
		//读取多光谱图像的红色，绿色，蓝色通道和全色图像
		poSrcDS1->GetRasterBand(1)->RasterIO(GF_Read, 0, 256 * (m - 1), imgXlen, y, buffTmpR, imgXlen, y, GDT_Float32, 0, 0);
		poSrcDS1->GetRasterBand(2)->RasterIO(GF_Read, 0, 256 * (m - 1), imgXlen, y, buffTmpG, imgXlen, y, GDT_Float32, 0, 0);
		poSrcDS1->GetRasterBand(3)->RasterIO(GF_Read, 0, 256 * (m - 1), imgXlen, y, buffTmpB, imgXlen, y, GDT_Float32, 0, 0);
		poSrcDS2->GetRasterBand(1)->RasterIO(GF_Read, 0, 256 * (m - 1), imgXlen, y, buffTmpP, imgXlen, y, GDT_Float32, 0, 0);

		for (i = 0; i < imgXlen * y; i++)
		{
			//将多光谱图像的R、G、B三个波段转换到IHS空间，得到H、S分量
			buffTmpH[i] = -sqrt(2.0f) / 6.0f*buffTmpR[i] - sqrt(2.0f) / 6.0f*buffTmpG[i] + sqrt(2.0f) / 3.0f*buffTmpB[i];
			buffTmpS[i] = 1.0f / sqrt(2.0f)*buffTmpR[i] - 1 / sqrt(2.0f)*buffTmpG[i];
			//用全色图像替代I分量，同H、S分量一起逆变换到RGB空间，得到融合图像
			buffTmpR[i] = buffTmpP[i] - 1.0f / sqrt(2.0f)*buffTmpH[i] + 1.0f / sqrt(2.0f)*buffTmpS[i];
			buffTmpG[i] = buffTmpP[i] - 1.0f / sqrt(2.0f)*buffTmpH[i] - 1.0f / sqrt(2.0f)*buffTmpS[i];
			buffTmpB[i] = buffTmpP[i] + sqrt(2.0f)*buffTmpH[i];
		}
		//数据写入poDstDS
		poDstDS->GetRasterBand(1)->RasterIO(GF_Write, 0, 256 * (m - 1), imgXlen, y, buffTmpR, imgXlen, y, GDT_Float32, 0, 0);
		poDstDS->GetRasterBand(2)->RasterIO(GF_Write, 0, 256 * (m - 1), imgXlen, y, buffTmpG, imgXlen, y, GDT_Float32, 0, 0);
		poDstDS->GetRasterBand(3)->RasterIO(GF_Write, 0, 256 * (m - 1), imgXlen, y, buffTmpB, imgXlen, y, GDT_Float32, 0, 0);
		m++;
	}
	//清除内存
	CPLFree(buffTmpR);
	CPLFree(buffTmpG);
	CPLFree(buffTmpB);
	CPLFree(buffTmpI);
	CPLFree(buffTmpH);
	CPLFree(buffTmpS);
	CPLFree(buffTmpP);
		
	//关闭dataset
	GDALClose(poSrcDS1);
	GDALClose(poSrcDS2);
	GDALClose(poDstDS);
	printf("succeed!\n");
	return 0;
}
```
