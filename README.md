# SWV
    pp so cool 
    
 

# Logic analyzer

-
# STM32CubeIDE/Monitor

-

# ADC
Week 2 : [https://classroom.google.com/u/0/c/NjUwNjQ5NTg1Nzc5/m/NjU3MTYxNzcwODMx/details](https://classroom.google.com/u/0/c/NjUwNjQ5NTg1Nzc5/m/NjU3MTYxNzcwODMx/details)
## **Overview**
Polling / Interrupt / DMA
**![](https://lh7-us.googleusercontent.com/sZqGI21Ldc6onuZQRmLO1h88v8vuAqPCmWRWPiQHXM7evSMBElAFOry6cyr-zRW1ggm2aG4hHq_pHAuOL2plyGaPSo1rgrxjWpW9LmbLZDWLhlBSbF626i3oP0W7nJXkhv02R0E4lGSIsRSlNFYrnD4)**

Poten เกือกม้า
**![](https://lh7-us.googleusercontent.com/MHK1BRydrj6ywACG_c1ObFhcZETPUfMHslPex5EqDsksWZRJKUic3a6VMziHFkMJJiDtbVbMTtxLy7stxUQKfbQtPM3G18Ko7SsjZYp92Y_nD7XYGxprkKgVwvENs7k-aBeg8hvWXaDsmr1Lf8DW15w)**
**![](https://lh7-us.googleusercontent.com/BDmINdzW1HOts8tfl0H1rO9qnmSCI_u9vE4w1_8epIMfDB8QxwHqoGSWfflOv1v-ai38fa5E4ebOVXc8AI_i8x4JiM2Q0LKm8_Xg-HqZ_TzQav61v17I8xgejlpQFOVCExmNiyklbR1OqSF065ajdhA)**
1. ต่อ ADC กับขา 2 potentiometer , ตั้งพอร์ต ADC input
2. NVIC Setting -> เปิด interrupt
## **ADC Interrupt**

 

    /* USER CODE BEGIN PV */
    struct _ADC_tag {
    ADC_ChannelConfTypeDef Config;
	uint16_t data;
	};
	uint8_t Current_Channel = 0;
กำหนด channel
```
/* USER CODE BEGIN PV */
// กำหนด channel
struct _ADC_tag ADC1_Channel[4] = {
{
.Config.Channel = ADC_CHANNEL_1,
.Config.Rank = ADC_REGULAR_RANK_1,
.Config.SamplingTime = ADC_SAMPLETIME_640CYCLES_5,
.Config.SingleDiff = ADC_SINGLE_ENDED,
.Config.OffsetNumber = ADC_OFFSET_NONE,
.Config.Offset = 0,
.data = 0
},
{
.Config.Channel = ADC_CHANNEL_2,
.Config.Rank = ADC_REGULAR_RANK_1,
.Config.SamplingTime = ADC_SAMPLETIME_640CYCLES_5,
.Config.SingleDiff = ADC_SINGLE_ENDED,
.Config.OffsetNumber = ADC_OFFSET_NONE,
.Config.Offset = 0,
.data = 0
},
{
.Config.Channel = ADC_CHANNEL_15,
.Config.Rank = ADC_REGULAR_RANK_1,
.Config.SamplingTime = ADC_SAMPLETIME_640CYCLES_5,
.Config.SingleDiff = ADC_SINGLE_ENDED,
.Config.OffsetNumber = ADC_OFFSET_NONE,
.Config.Offset = 0,
.data = 0
},
{
.Config.Channel = ADC_CHANNEL_TEMPSENSOR_ADC1,
.Config.Rank = ADC_REGULAR_RANK_1,
.Config.SamplingTime = ADC_SAMPLETIME_640CYCLES_5,
.Config.SingleDiff = ADC_SINGLE_ENDED,
.Config.OffsetNumber = ADC_OFFSET_NONE,
.Config.Offset = 0,
.data = 0
}
};
```
```
/* USER CODE BEGIN 2 */
HAL_ADCEx_Calibration_Start(&hadc1, ADC_SINGLE_ENDED);
HAL_ADC_Start_IT(&hadc1);
```
```
/* USER CODE BEGIN 3 */
//just mimic micrcontroller task
HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
HAL_Delay(500);
}
/* USER CODE END 3 */
```
```
/* USER CODE BEGIN 4 */
void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef *hadc)
{
if(hadc == &hadc1)
{
	ADC1_Channel[Current_Channel].data= HAL_ADC_GetValue(&hadc1);
	//change channel
	Current_Channel++;
	Current_Channel %= 4;
	HAL_ADC_ConfigChannel(&hadc1,&ADC1_Channel[Current_Channel].Config);
	//start next ADC
	HAL_ADC_Start_IT(&hadc1);
}
}
```
# DMA (ADC)
> ตั้ง DMA ตึงๆ

ตั้งจำนวน Channel ADC ที่อ่าน , ปรับ Sampling time
**![](https://lh7-us.googleusercontent.com/-v6Crr_SYBtTl3fgtxTA24nM5KPJBV8RrjE1BwgiTARTh_uqJtXg1gduOf_Z7IzU1cjCyKrmnb1vEHUnHvcpZhGsOzcpH8ssNnytsWzuB9DQ3fa8DjlNP9rkys6_bmPLnqo52uWg8LnBrnHSA0WQZNM)**
 half 16 & word 32
 **![](https://lh7-us.googleusercontent.com/yjYyfMGZtCUHph_b4VYMF9yaHgadAd59CVad7_OdTwYkqCCQYRb15D646NTR1szR33ESXzHAy8pKG5IekMYpWgVZqBDqPYa5Uza-KwDR0xb8s5QLo8ev2ZTzUhYfRo60UhhP3l6Tli2VylWDr9LM4OM)**

 

> **Scan Conversion**
> able you to use one DAC for multiple channel.
> - Continuous conversion -- once triggered, the ADC will never stop.
> 
>  **Sampling Time**
The sampling time is the ADC clock cycles for which the sample and hold capacitor is charged up to the channel input voltage. The ADC then requires another 12 clock cycles to convert to 12 bits accuracy.
> - The programmable range in sampling time is needed to accommodate a range if input signal impedance, since higher input impedance take longer to charge up the capacitor.
[reference](https://community.st.com/t5/stm32-mcus-products/what-is-the-sampling-time-in-adc/td-p/379519)
> 
> **Buffer**

**Buffer**
Buffer is an *array*
Buffer Normal is like a straight line with data filling from start to end.
Buffer Circular is like a ring that can infinity store data (CPU have to read before data get replace by a new one)



 สร้างช่องเก็บ 40 ช่อง
 ```
 /* USER CODE BEGIN PV */
 
uint16_t ADC_RawRead[40]={0};
HAL_ADC_Start_DMA(&hadc1, ADC_RawRead, 40);
```
```
/* USER CODE BEGIN 2 */

HAL_ADCEx_Calibration_Start(&hadc1, ADC_SINGLE_ENDED);
HAL_ADC_Start_IT(&hadc1);
HAL_ADC_Start_DMA(&hadc1, ADC_RawRead, 40);
```
# TIMER
Week 3 : https://classroom.google.com/c/NjUwNjQ5NTg1Nzc5/m/NjU4MDMyNzgyMzkx/details

> Timer 2, 5 – 32 bit (ที่เหลือ 16 bit)

## **Setup Timer period callback**

 1. **Enable interrupt**
 **![](https://lh7-us.googleusercontent.com/C9TUuVvMioCAxecwwqs6mWmmwpr0DjJkJPp1uBC2JD_as0Ze9jQ7UQBrIU1Evz7pWB6c2VQjKKgDZfYJDbfDOHiRmUgSm7giZBfnjVI9YP3PDwsArygJEa3I7J56uEbXzs3JkmWsVINkipnkp0y-w8U)**
 ```
/* USER CODE BEGIN 2 */

HAL_TIM_Base_Start(&htim2);
```
```
/* USER CODE BEGIN 4 */

void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
	if(htim == &htim3)
{
HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
}
}
```
## **Setup Timer event and DMA**

 1. Check circular
 2. Set Timer Trigger Out event
**![](https://lh7-us.googleusercontent.com/0QR0IvWjPWRXyQ_Sx2BRAM9MF0MfC5EcDn0SX0HqKb1sWQ5kqBrfAa4JtTAhp-MLFCY86ZDpN85RcT8p9gQpOEj7KgkpnqgoCdGA_X56_zw5u3feB356CX9uDzTPZWx8x-fevVC2BuY7lsycqxx3GyE)**
 3. Set DMA continuous request
 **![](https://lh7-us.googleusercontent.com/7g7r_xRL02e9R9B1sGRWuykvKJedh96zh9bmIF3dQ1lGuwRT2Q2wJtEL_83QuaH72ROw94hyT9kQtiiNq4zbv8v7kZWDfzhTz3eB0AzDMBonnv1xE3-jk6SiFs3Bew5rYt5to9bTb4vqWuqRmLIZM2k)**
 4. Set Trigger out event to Update event
 **![](https://lh7-us.googleusercontent.com/Apg4YVXJakqiLv96EHZwkShtHxgggB4fRU5z1e6j438hxjLGad3aP5-leO2altgRufpB06R0xZrBP9VwRZaOL6HkQ4SxmyL7YD6INDFs3XcMw81agIYhPGRjzQv5Juyrh8yLcTNy6O3fVx-9B1Ed8Mc)**
```
/* USER CODE BEGIN PV */

uint16_t ADCBuffer[10];
```
```
/* USER CODE BEGIN 2 */

HAL_ADC_Start_DMA(&hadc1, ADCBuffer, 10);
HAL_TIM_Base_Start(&htim3);
```
# **OUTPUT Compare**
Week 4 : https://classroom.google.com/c/NjUwNjQ5NTg1Nzc5/m/NjQ4MTYwNzk0MjM5/details
```
/* USER CODE BEGIN 2 */

HAL_TIM_Base_Start(&htim3);
HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);
/* USER CODE END 2 */
```

```
/* USER CODE BEGIN 3 */

__HAL_TIM_SET_COMPARE(&htim3, TIM_CHANNEL_1, 2000);
```
**![](https://lh7-us.googleusercontent.com/A9LbDAhxsOJwEGAf_YL7CuatM5oor47rp2Do1brc76W6Kd3fNfoClmRrpJ81y_oMoO0BOgLYpkughqE9k57Sh7zcJ5djJyu02exKnSJpYgwH-lE4aLuHoa_FlmMAkZb9njeVgGqOXWDQtgJKpMnQ-Ig)**
**![](https://lh7-us.googleusercontent.com/8GzRi6hvrXAFLrreW2R_TSN6jVcjJ_NgX3vpbi0Q_sCE0ioYV9nH4GXvEZCcMfB4IjmIt0Z5PjZi3X4NYG18BHfzhfiT-u9L6cct-fRwshGz6M16ynXoztCG1qRdfxCwrNUtNgIK31b8wIShpg3cSe8)**

# Input Capture
**![](https://lh7-us.googleusercontent.com/fNZAjVqe-CgNazwlcJja8kPjbRWUmeIaMnvKwPRF2EjLUZhU-Vi59mkQAYFNL7NTF5oD6ANDtYXcwGCJVal7-BvE5O4vQ3Ual3tjD18m7NaLUHNF4YdlraFZ-r7do_ihu0tPqiUSc4Az-wbXm6w2u6U)**
**![](https://lh7-us.googleusercontent.com/SZ5PffqFbbXQP_Z-pdvmYgVC3ALedicLzucE02w-D86PfvierA0wXq-OWXNVafpjmiWyo2MGjB6nTDKoo5Su38nMaSXgIawYpJOcNoa1bjQ0L7gBhV8Wb-zW3v0H5vFL9n2_wevA-1tn4T7I64rFQsI)**
```
/* USER CODE BEGIN 2 */

HAL_TIM_Base_Start(&htim1);
HAL_TIM_IC_Start(&htim1, TIM_CHANNEL_1);
HAL_TIM_IC_Start(&htim1, TIM_CHANNEL_2);
```
```
/* USER CODE BEGIN 3 */

Rise = __HAL_TIM_GET_COMPARE(&htim1,TIM_CHANNEL_1);

Fall = __HAL_TIM_GET_COMPARE(&htim1,TIM_CHANNEL_2);
dt = Fall - Rise;
	if(Rise>Fall) {
		ServoTime = dt + 65535 ;
	}
	else {
		ServoTime = dt;
	}
```

#  Blue button (PB13)

**![](https://lh7-us.googleusercontent.com/4M8AhBIITNfF0-b3tcZuU0DVTqvil-qhI-Mm2y4_bj56OREwEH567zlSC7T502RGVMFOD7NcLKVu6nfq-SvLVCUHnxeujz-V3ORWOfSlUKRLvL5b3gV-6CHl22Z8zCDWTjqmffvZxbLIuflN8cEwi48)**
**![](https://lh7-us.googleusercontent.com/51yJn4G58UB1LvnGFmfOqi28u-P7NS4qsA65GIco_vPvDixTkzhQ_f4EE-QEzVkqPHzw41_u07KPAdZ6oy1WJuK2m9_vUPAruD9FoJ_IDS1x6CvcWrsCNrNg9W-C6pHGIh4eG7kZlQomzCRLzq1ufcQ)**
```
/* USER CODE BEGIN 4 */

void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
	if(GPIO_Pin == GPIO_PIN_13)
		{
			//your code//
		}
}
```
