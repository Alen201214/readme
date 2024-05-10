#include "include.h"
#include "math.h"

uint32_t WriteFlashData = 0x12345678;
uint32_t PageError = 0;
uint16_t Flash_Check=0;
extern uint16_t first[1];
/*初始化FLASH_EraseInitTypeDef*/
FLASH_EraseInitTypeDef FlashSet;

void ResetFlash(void)
{

uint32_t addr=FLASH_USER_START_ADDR;
/* Unlock the Flash to enable the flash control register access *************/ 
	HAL_FLASH_Unlock();
////  __HAL_FLASH_CLEAR_FLAG(FLASH_FLAG_EOP | FLASH_FLAG_BSY | FLASH_FLAG_WRPERR | 
////                          FLASH_FLAG_PGAERR | FLASH_FLAG_RDERR );
//	  __HAL_FLASH_CLEAR_FLAG(FLASH_FLAG_EOP | FLASH_FLAG_WRPERR | 
//                         FLASH_FLAG_PGAERR);
	
	/*擦除方式页擦除FLASH_TYPEERASE_PAGES，块擦除FLASH_TYPEERASE_MASSERASE*/
	FlashSet.TypeErase = FLASH_TYPEERASE_PAGES;
	
	/*擦除地址*/
	//FlashSet.Page = FLASH_USER_START_ADDR;//修改
	
	/*擦除页数*/
	FlashSet.NbPages = (FLASH_USER_END_ADDR - FLASH_USER_START_ADDR)/FLASH_PAGE_SIZE;
	
	/*设置PageError，调用擦除函数*/
	if(HAL_FLASHEx_Erase(&FlashSet, &PageError)!=HAL_OK)
  while (1)
	{

	}
//	if (FLASH_ErasePage(FLASH_USER_START_ADDR )!= FLASH_COMPLETE)
//	while (1)
//	{
//	}
	/* 3/4对FLASH烧写*/
		addr=0x08005800;
	 while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD, addr, 0x12345678)!= HAL_OK);
   while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD, addr+=22, 0)!= HAL_OK);
	 HAL_FLASH_Lock();


}
/*FLASH写入程序*/
void writeFlash(void)
{
uint32_t addr=FLASH_USER_START_ADDR;
/* Unlock the Flash to enable the flash control register access *************/ 
	HAL_FLASH_Unlock();
////  __HAL_FLASH_CLEAR_FLAG(FLASH_FLAG_EOP | FLASH_FLAG_BSY | FLASH_FLAG_WRPERR | 
////                          FLASH_FLAG_PGAERR | FLASH_FLAG_RDERR );
//	  __HAL_FLASH_CLEAR_FLAG(FLASH_FLAG_EOP |  FLASH_FLAG_WRPERR | 
//                         FLASH_FLAG_PGAERR );

	
	/*擦除方式页擦除FLASH_TYPEERASE_PAGES，块擦除FLASH_TYPEERASE_MASSERASE*/
	FlashSet.TypeErase = FLASH_TYPEERASE_PAGES;
	
	/*擦除地址*/
//	FlashSet.Page = FLASH_USER_START_ADDR;
	
	/*擦除页数*/
	FlashSet.NbPages = (FLASH_USER_END_ADDR - FLASH_USER_START_ADDR)/FLASH_PAGE_SIZE;
//	
//	/*设置PageError，调用擦除函数*/
	
	if(HAL_FLASHEx_Erase(&FlashSet, &PageError)!=HAL_OK)
  while (1)
	{

	}
	/* 3/4对FLASH烧写*/

	while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD, addr, 0x12345678)!= HAL_OK);
  while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD, addr+=22, first[0])!= HAL_OK);
	/* 4/4锁住FLASH*/
	HAL_FLASH_Lock();

}


/*FLASH读取打印程序*/
void ReadFlash(void)
{
	u32 internal_flash_save_flag[1];
  uint32_t addr=FLASH_USER_START_ADDR;
	internal_flash_save_flag[0]= *(__IO uint32_t *)(addr);	//addr+=4;
  first[0] = *(__IO uint32_t*)(addr+=22);
		if(internal_flash_save_flag[0]!=0x12345678)
		{
//		ResetFlash();
//		ReadFlash();
		}

}


void FlashCheck()
{

	u16 i =0;
	Flash_Check=0;
	for(i=0;i<256;i++)
	Flash_Check+=*(__IO uint32_t*)(0x8000000+i*4);
	for(i=0;i<256;i++)
	Flash_Check+=*(__IO uint32_t*)(0x8001000+i*4);
	for(i=0;i<256;i++)
	Flash_Check+=*(__IO uint32_t*)(0x8002000+i*4);
	for(i=0;i<256;i++)
	Flash_Check+=*(__IO uint32_t*)(0x8003000+i*4);
}


// /**********************************************************************************
//  * 函数功能: 页擦除
//  * 输入参数: 无
//  * 返 回 值: 无
//  * 说    明：无 
//  */
//void Flash_Erase(void)
//{  	
//    FlashSet.TypeErase   = FLASH_TYPEERASE_PAGES;
//    FlashSet.Page = FLASH_USER_START_ADDR;
//    FlashSet.NbPages     = (FLASH_USER_END_ADDR - FLASH_USER_START_ADDR) / FLASH_PAGE_SIZE;
//    
//     if(HAL_FLASHEx_Erase(&FlashSet, &PageError)!=HAL_OK)
//	 {
//		 HAL_FLASH_Lock();  
//		 printf(" Error...1\r\n");
//         Error_Handler( );
//	 }
//}
// /**********************************************************************************
//  * 函数功能: 数据写入
//  * 输入参数: 写入数据缓存数组指针、写入数据数
//  * 返 回 值: 无
//  * 说    明：无 
//  */    
//void Flash_Write(u32 *pBuffer,u32  NumToWrite)
//{
// 
//    u16  i=0;
//    u32 Address = FLASH_USER_START_ADDR;
//    HAL_FLASH_Unlock();	    //解锁
//    Flash_Erase( );         //先擦除
//                            //再写入
//       printf(" 擦除完成，准备写入......\r\n");
//     while ( (Address < FLASH_USER_END_ADDR) && (i<NumToWrite)  )    
//    {
//        if (HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD, Address, pBuffer[i]) == HAL_OK)
//        {
//            Address = Address + 4;  //地址后移4个字节
//            i++;
//        }
//        else
//		{  
//			printf(" Error...2\r\n"); 
//            Error_Handler( );            
//		}
//    }
//  
//    HAL_FLASH_Lock();   //上锁
// 
// 
//}
///**********************************************************************************
//  * 函数功能: 数据读取
//  * 输入参数: 读取数据缓存数组指针、读出数据数
//  * 返 回 值: 无
//  * 说    明：无
//  */
//void Flash_Read(u32  *pBuffer,u32  NumToRead)
//{
//    u16  i=0;
//    u32 Address = FLASH_USER_START_ADDR;
//    
//    while ( (Address < FLASH_USER_END_ADDR) && (i<NumToRead)  )
//    {
//        pBuffer[i++]= *(__IO u32 *)Address;  
//        Address = Address + 4;   //地址后移4个字节
//    }
//  
//}
//void ResetInf(void)
//{
//	uint32_t addr=FLASH_USER_START_ADDR;
//	HAL_FLASH_Unlock();
//	Flash_Erase();
//	while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD,addr+=4,0x12345678));					//初始ID
//	while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD,addr+=8,1000));       //Int_Lora_SPEED   7-12
//	HAL_FLASH_Lock();
//}
//void WriteInf(void)
//{
//	uint32_t addr=FLASH_USER_START_ADDR;
//	HAL_FLASH_Unlock();
//	Flash_Erase();
//	while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD,addr+=4,0x12345678));
//	while(HAL_FLASH_Program(FLASH_TYPEPROGRAM_DOUBLEWORD, addr+=8, first[0])!= HAL_OK);
//	
//	HAL_FLASH_Lock();
//}
//void ReadInf(void)
//{
//	u32 dateread[2];		//4个字节的存储信息
//	u32 addr = FLASH_USER_START_ADDR;
//	dateread[0] = *(__IO uint32_t *)(addr);	addr+=4;
//	first[0]= *(__IO uint32_t *)(addr);	addr+=8;



//	if(dateread[0]!=0x12345678)	//如果是新的内存块
//	{
//		ResetInf();
//		ReadInf(); 
//	}
//}
// 