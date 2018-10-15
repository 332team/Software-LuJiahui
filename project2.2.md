```
#include "pch.h"
#include <iostream>
using namespace std;
#include "./gdal/gdal_priv.h"
#pragma comment(lib,"gdal_i.lib")


int main()
{
	GDALDataset* poSrcDS;
	GDALDataset* poDstDS;
	int imgXlen, imgYlen, StartX1, StartY1, StartX2, StartY2, tmpXlen1, tmpYlen1, tmpXlen2, tmpYlen2;
	const char* srcPath = "square.jpg";
	const char* dstPath = "res2.tif";
	GByte* buffTmp;
	int i, bandNum, j, k;

	GDALAllRegister();

	poSrcDS = (GDALDataset*)GDALOpenShared(srcPath, GA_ReadOnly);

	imgXlen = poSrcDS->GetRasterXSize();
	imgYlen = poSrcDS->GetRasterYSize();
	bandNum = poSrcDS->GetRasterCount();

	cout << "Image X Length:" << imgXlen << endl;
	cout << "Image Y Length:" << imgYlen << endl;
	cout << "Band number:   " << bandNum << endl;


	buffTmp = (GByte*)CPLMalloc(imgXlen*imgYlen * sizeof(GByte));
	poDstDS = GetGDALDriverManager()->GetDriverByName("GTiff")->Create(dstPath, imgXlen, imgYlen, bandNum, GDT_Byte, NULL);
	for (i = 0; i < bandNum; i++)
	{
		poSrcDS->GetRasterBand(i + 1)->RasterIO(GF_Read, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
		poDstDS->GetRasterBand(i + 1)->RasterIO(GF_Write, 0, 0, imgXlen, imgYlen, buffTmp, imgXlen, imgYlen, GDT_Byte, 0, 0);
		printf("……band %d processing……\n", i);
	}

	StartX1 = 300;
	StartY1 = 300;
	tmpXlen1 = 100;
	tmpYlen1 = 50;
	buffTmp = (GByte*)CPLMalloc(tmpXlen1*tmpYlen1 * sizeof(GByte));

	poSrcDS->GetRasterBand(1)->RasterIO(GF_Read, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);
	for (j = 0; j < tmpYlen1; j++)
	{
		for (k = 0; k < tmpXlen1; k++)
		{
			buffTmp[j*tmpXlen1 + k] = (GByte)255;
		}
	}
	poDstDS->GetRasterBand(1)->RasterIO(GF_Write, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);

	poSrcDS->GetRasterBand(2)->RasterIO(GF_Read, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);
	for (j = 0; j < tmpYlen1; j++)
	{
		for (k = 0; k < tmpXlen1; k++)
		{
			buffTmp[j*tmpXlen1 + k] = (GByte)255;
		}
	}
	poDstDS->GetRasterBand(2)->RasterIO(GF_Write, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);

	poSrcDS->GetRasterBand(3)->RasterIO(GF_Read, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);
	for (j = 0; j < tmpYlen1; j++)
	{
		for (k = 0; k < tmpXlen1; k++)
		{
			buffTmp[j*tmpXlen1 + k] = (GByte)255;
		}
	}
	poDstDS->GetRasterBand(3)->RasterIO(GF_Write, StartX1, StartY1, tmpXlen1, tmpYlen1, buffTmp, tmpXlen1, tmpYlen1, GDT_Byte, 0, 0);

	StartX2 = 500;
	StartY2 = 500;
	tmpXlen2 = 50;
	tmpYlen2 = 100;
	buffTmp = (GByte*)CPLMalloc(tmpXlen2*tmpYlen2 * sizeof(GByte));
	poSrcDS->GetRasterBand(1)->RasterIO(GF_Read, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);
	for (j = 0; j < tmpYlen2; j++)
	{
		for (k = 0; k < tmpXlen2; k++)
		{
			buffTmp[j*tmpXlen2 + k] = (GByte)0;
		}
	}
	poDstDS->GetRasterBand(1)->RasterIO(GF_Write, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);

	poSrcDS->GetRasterBand(2)->RasterIO(GF_Read, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);
	for (j = 0; j < tmpYlen2; j++)
	{
		for (k = 0; k < tmpXlen2; k++)
		{
			buffTmp[j*tmpXlen2 + k] = (GByte)0;
		}
	}
	poDstDS->GetRasterBand(2)->RasterIO(GF_Write, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);

	poSrcDS->GetRasterBand(3)->RasterIO(GF_Read, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);
	for (j = 0; j < tmpYlen2; j++)
	{
		for (k = 0; k < tmpXlen2; k++)
		{
			buffTmp[j*tmpXlen2 + k] = (GByte)0;
		}
	}
	poDstDS->GetRasterBand(3)->RasterIO(GF_Write, StartX2, StartY2, tmpXlen2, tmpYlen2, buffTmp, tmpXlen2, tmpYlen2, GDT_Byte, 0, 0);


	CPLFree(buffTmp);
	GDALClose(poDstDS);
	GDALClose(poSrcDS);
	system("PAUSE");
	return 0;
}
```
