
/******************************************************************************
*
* Copyright (C) 2009 - 2014 Xilinx, Inc.  All rights reserved.
*
* Permission is hereby granted, free of charge, to any person obtaining a copy
* of this software and associated documentation files (the "Software"), to deal
* in the Software without restriction, including without limitation the rights
* to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
* copies of the Software, and to permit persons to whom the Software is
* furnished to do so, subject to the following conditions:
*
* The above copyright notice and this permission notice shall be included in
* all copies or substantial portions of the Software.
*
* Use of the Software is limited solely to applications:
* (a) running on a Xilinx device, or
* (b) that interact with a Xilinx device through a bus or interconnect.
*
* THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
* IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
* FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
* XILINX  BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
* WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF
* OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
* SOFTWARE.
*
* Except as contained in this notice, the name of the Xilinx shall not be used
* in advertising or otherwise to promote the sale, use or other dealings in
* this Software without prior written authorization from Xilinx.
*
******************************************************************************/

/*
 * helloworld.c: simple test application
 *
 * This application configures UART 16550 to baud rate 9600.
 * PS7 UART (Zynq) is not initialized by this application, since
 * bootrom/bsp configures it to baud rate 115200
 *
 * ------------------------------------------------
 * | UART TYPE   BAUD RATE                        |
 * ------------------------------------------------
 *   uartns550   9600
 *   uartlite    Configurable only in HW design
 *   ps7_uart    115200 (configured by bootrom/bsp)
 */

#include <stdio.h>
#include <math.h>
#include "platform.h"
#include "xil_printf.h"
#include "xil_types.h"
#include "xstatus.h"
#include "xllfifo.h"
#include "xparameters.h"

typedef struct
{
//	XLlFifo fifo_spi;
	XLlFifo fifo_i2s;
	u8 chipAddr;
	int wordSize;
} max98357a_config;

/*int adau1761_init(adau1761_config *pDevice, u32 DeviceId);//, u32 DeviceId2);
int adau1761_checkInit(adau1761_config *pDevice);
void adau1761_write(adau1761_config *pDevice,u16 addr, u8 wdata);
u8 adau1761_read(adau1761_config *pDevice,u16 addr);
*/
int max98357a_init(max98357a_config *pDevice, u32 DeviceId);
int max98357a_checkInit(max98357a_config *pDevice);
void max98357a_write(max98357a_config *pDevice,u16 addr, u8 wdata);
u8 max98357a_read(max98357a_config *pDevice,u16 addr);

//void adau_write();
//void adau_read();
int max98357a_init(max98357a_config *pDevice, u32 DeviceId) // u32 DeviceId2)
{
	pDevice->chipAddr = 0;
	pDevice->wordSize = 4;
	int xStatus;
	XLlFifo_Config *pConfig = XLlFfio_LookupConfig(DeviceId);
	// xStatus = XLlFifo_CfgInitialize(&pDevice->pConfig,pConfig,pConfig->BaseAddress);
	//	int xStatus = XLlFifo_CfgInitialize(&pDevice->fifo_spi,pConfig,pConfig->BaseAddress);

	if(XST_SUCCESS != xStatus) {
		return -1;
	}

	// Check for the Reset value
	//u32 Status = XLlFifo_Status(&pDevice->fifo_spi);
	//XLlFifo_IntClear(&pDevice->fifo_spi,0xffffffff);
	//Status = XLlFifo_Status(&pDevice->fifo_spi);
	if(xStatus != 0) {
		return -2;
	}

/*	pConfig = XLlFfio_LookupConfig(DeviceId2);
	xStatus = XLlFifo_CfgInitialize(&pDevice->fifo_i2s,pConfig,pConfig->BaseAddress);
	if(XST_SUCCESS != xStatus) {
		return -4;
	}
*/
	// Check for the Reset value
	xStatus = XLlFifo_Status(&pDevice->fifo_i2s);
	XLlFifo_IntClear(&pDevice->fifo_i2s,0xffffffff);
	xStatus = XLlFifo_Status(&pDevice->fifo_i2s);
	if(xStatus != 0) {
		return -5;
	}

	// This enables SPI mode
	//adau1761_read(pDevice, 0x4000);
	//adau1761_read(pDevice, 0x4000);
	//adau1761_read(pDevice, 0x4000);

	// Enable clock
	max98357a_write(pDevice, 0x4000, 0x01);

	// SLEWPD=1, ALCPD=1, DECPD=1, SOUTPD=1, INTPD=1, SINPD=1, SPPD=1
	max98357a_write(pDevice, 0x40F9, 0x7F);
	// CLK1=0, CLK0=1
	max98357a_write(pDevice, 0x40FA, 0x01);

	// MX3LM=1, MX3RM=0, MX3G1=0, MX3G2=0, MX3AUXG=0, MX5G3=3, MX6G3=0, LOUTVOL=63
	// MX4LM=0, MX4RM=1, MX4G1=0, MX4G2=0, MX4AUXG=0, MX5G4=0, MX6G4=3, ROUTVOL=63

	// LRCLK/LRPOL=falling edge, LRCLK/LRMOD=50%, BCLK/BPOL=falloing edge, LRDEL=1
	// SPSRS=0, LRMOD=0, BPOL=0, LRPOL=0, CHPF=0, MS=0
	max98357a_write(pDevice, 0x4015, 0x00);
	// BPF=0, ADTDM=0, DATDM=0, MSBP=0, LRDEL=0
	max98357a_write(pDevice, 0x4016, 0x00);
	// DAPAIR=0, DAOSR=0, ADOSR=0, CONVSR=0
	max98357a_write(pDevice, 0x4017, 0x00);
	// MX3RM=0, MX3LM=1, MX3AUXG=0, MX3EN=1
	max98357a_write(pDevice, 0x401C, 0x21);
	// MX3G2=0, MX3G1=0
	max98357a_write(pDevice, 0x401D, 0x00);
	// MX4RM=1, MX4LM=0, MX4AUXG=0, MX4EN=1
	max98357a_write(pDevice, 0x401E, 0x41);
	// MX4G2=0, MX4G1=0
	max98357a_write(pDevice, 0x401F, 0x00);
	// MX5G4=0, MX5G3=10, MX5EN=1
	max98357a_write(pDevice, 0x4020, 0x05);
	// MX6G4=01, MX6G3=0, MX6EN=1
	max98357a_write(pDevice, 0x4021, 0x11);
	// MX7=0, MX7EN=0
	max98357a_write(pDevice, 0x4022, 0x00);
	// LOUTVOL=63, LOUTM=1, LOMODE=0
	max98357a_write(pDevice, 0x4025, 0xFE);
	// ROUTVOL=63, ROUTM=1, ROMODE=0
	max98357a_write(pDevice, 0x4026, 0xFE);

	// HPBIAS=0, DACBIAS=0, PBIAS=0, PREN=1, PLEN=1
	max98357a_write(pDevice, 0x4029, 0x03);
	// DACMONO=0, DACPOL=0,DEMPH=0, DACEN=3
	max98357a_write(pDevice, 0x402A, 0x03);

	// SINRT=1
	max98357a_write(pDevice, 0x40F2, 0x01);

	return max98357a_checkInit(pDevice);
}

int max98357a_checkInit(max98357a_config *pDevice) {
	u8 rdata = max98357a_read(pDevice, 0x4000);

	if (rdata!=0x01) {
		return -3;
	}

	return 0;
}

 void max98357a_write(max98357a_config *pDevice,u16 addr, u8 wdata) {
/*	XLlFifo_TxPutWord(&pDevice->fifo_spi, (pDevice->chipAddr<<1) & 0xFF);
	XLlFifo_TxPutWord(&pDevice->fifo_spi, (addr>>8) & 0xFF );
	XLlFifo_TxPutWord(&pDevice->fifo_spi, addr & 0xFF );
	XLlFifo_TxPutWord(&pDevice->fifo_spi, wdata );
	XLlFifo_iTxSetLen(&pDevice->fifo_spi, 4 * pDevice->wordSize);
	while(XLlFifo_RxOccupancy(&pDevice->fifo_spi)!=4) {}
	XLlFifo_RxGetWord(&pDevice->fifo_spi);
	XLlFifo_RxGetWord(&pDevice->fifo_spi);
	XLlFifo_RxGetWord(&pDevice->fifo_spi);
	XLlFifo_RxGetWord(&pDevice->fifo_spi);*/
}

u8 max98357a_read(max98357a_config *pDevice,u16 addr) {
	/*XLlFifo_TxPutWord(&pDevice->fifo_spi, ((pDevice->chipAddr<<1) |0x01) & 0xFF);
	XLlFifo_TxPutWord(&pDevice->fifo_spi, (addr>>8) & 0xFF );
	XLlFifo_TxPutWord(&pDevice->fifo_spi, addr & 0xFF );
	XLlFifo_TxPutWord(&pDevice->fifo_spi, 0 );
	XLlFifo_iTxSetLen(&pDevice->fifo_spi, 4 * pDevice->wordSize);
	while(XLlFifo_RxOccupancy(&pDevice->fifo_spi)!=4) {}
	XLlFifo_RxGetWord(&pDevice->fifo_spi);
	XLlFifo_RxGetWord(&pDevice->fifo_spi);
	XLlFifo_RxGetWord(&pDevice->fifo_spi);
	u32 rdata = XLlFifo_RxGetWord(&pDevice->fifo_spi);*/

//	return (u8)(rdata & 0xFF);
}

void max98357a_i2s_write(max98357a_config *pDevice,u16 left,u16 right) {
	while ( !XLlFifo_iTxVacancy(&pDevice->fifo_i2s) ) {
		printf("I2S FIFO full. Waiting ... \n\r");
	}
	XLlFifo_TxPutWord(&pDevice->fifo_i2s, ((u32)left<<16) | (u32)right);
	XLlFifo_iTxSetLen(&pDevice->fifo_i2s, 1 * pDevice->wordSize);
}

// 128 samples = 39.0625 kHz/128 = 305Hz
#define PERIODSAMPLES 128

int main()
{
    init_platform();
    max98357a_config codec;
  //  print("Hello World\n\r");

	//print("Initializing ADAU1761 ... ");
  //  max98357a_config codec;
 //   if (adau1761_init(&codec, XPAR_AXI_FIFO_MM_S_0_DEVICE_ID, XPAR_AXI_FIFO_MM_S_1_DEVICE_ID)==0)

    if (max98357a_init(&codec, XPAR_AXI_FIFO_MM_S_0_DEVICE_ID)==0)
    	//if (max98357a_init(&codec, XPAR_AXI_FIFO_MM_S_0_DEVICE_ID)==0)
    {
    	print("OK\n\r");
    }
    else {
    	print("FAILED\n\r");
    }

    printf("Number of available FIFO entries: %d\n\r",(int)XLlFifo_iTxVacancy(&codec.fifo_i2s));
    //print("Press RETURN\n\r");
    getchar();

    short left[PERIODSAMPLES];
    short right[PERIODSAMPLES];
    double amp = 16384;
    for(int i=0;i<PERIODSAMPLES;++i) {
    	left[i] = (short) (cos((double)i/PERIODSAMPLES*2*M_PI) * amp);
    	right[i] = (short) (sin((double)i/PERIODSAMPLES*2*M_PI) * amp);
    }

    for(;;) {
		for(int i=0;i<PERIODSAMPLES;++i) {
			max98357a_i2s_write(&codec,(u16)left[i],(u16)right[i]);
		}
    }

	//print("Good bye\n\r");

    cleanup_platform();
    return 0;
}
