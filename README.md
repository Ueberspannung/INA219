# INA219
simple interface to INA219 voltage current and power monitor

##### Table of contents
- [introduction](#introduction)
- [INA219 class](#INA219-class)
- [interface](#interface)
    - [INA219            ](#INA219            )
    - [conversion_ready  ](#conversion_ready  )  
    - [clear_ready_flag  ](#clear_ready_flag  ) 
    - [overflow          ](#overflow          ) 
    - [getCurrent        ](#getCurrent        )
    - [getShuntVoltage   ](#getShuntVoltage   ) 
    - [getPower          ](#getPower          )
    - [setCalibration    ](#setCalibration    )
    - [getCalibration    ](#getCalibration    )
    - [reset             ](#reset             )
    - [setBusVoltageRange](#setBusVoltageRange)
    - [setAdcResolution  ](#setAdcResolution  )
    - [setAdcAveraging   ](#setAdcAveraging   )
    - [setShuntGain      ](#setShuntGain      )
    - [setOperationMode  ](#setOperationMode  )			

  
## introduction
The INA219 is a combined voltage, current and power monitoren chip by [Texas Instrument](https://www.ti.com).  
It is controlled via an I2C Interface and features a 12 bit ADC resulting a Resolution of ~10 µV for shunt voltage (current measurement) and 4 mV for voltage measurement.  
There are similar chips eg. the INA232 which is pin compatible but features a 16 bit ADC or the INA3221 which is a triple version with 13 bit ADC.  

There are ready to use circuit boards available:  
![INA219](./img/INA219-PCBA.jpg)  



## INA219 class
The INA219 class offers a simple interface closely related to register and function naming in the datasheet.
It has been build around the Arduino Framework and makes use of the wire library.


### interface
```
INA219(uint8_t addr);

bool conversion_ready(uint16_t * pBusVoltage_mV);
void clear_ready_flag(void);
bool overflow(void);

int16_t getCurrent(void);
int16_t getShuntVoltage(void);
uint16_t getPower(void);

void setCalibration(uint16_t Cal);
uint16_t getCalibration(void);

void reset(void);
void setBusVoltageRange(bus_voltage_range_et range);
void setAdcResolution(	adc_selector_et adc,
						adc_res_et resolution);
void setAdcAveraging(	adc_selector_et adc,
						uint8_t scale);	// 0-7 = averaging 2^scale samples
void setShuntGain(		gain_et gain);
void setOperationMode(	op_mode_et mode);				
```

There are only a few functions needed to control the chip. The only value guaranteed to be a mV value is the bus voltage.  
The shunt voltage is read as internal value which is a multiple of 10 µV.  
The accuracy and base of the current and power reading depends on the shunt resistor. 
The shunt resistor and the desired full scale reading leads to the calibration value 
which needs to be programmed to the INA219. See datasheet for details.
The power reading is the the product of the bus voltage register and the current register divided by 5000.

#### INA219
constructor, needs to be called with the chip I2C adress

#### conversion_ready
returns true when a conversion cycle is completed.  
The busVoltage is valid and Current and power can be read.  

#### clear_ready_flag
is equal to getPower.  
The read flag is cleared whenever the power register is read or a new opration mode is set.  


#### overflow
returns overflow flag which is set on an math overflow calculating the power register  


#### getCurrent
gets the current reading calculatet from the shunt voltage and the calibration register

#### getShuntVoltage
reads the shunt voltage in 10 µV units

#### getPower
reads the power register based on the bus voltage and current value

#### setCalibration
sets the calibration register.  
the value has to be calculated according to the datasheet  

#### getCalibration
reads the calibration register

#### reset
performs a chip reset

#### setBusVoltageRange
the bus voltage range can be either 16 V or 32 V {	BUS_VOLTAGE_16V, BUS_VOLTAGE_32V }


#### setAdcResolution
the ADC resolution can be 9 - 12 bits
{ ADC_RES_9BIT, ADC_RES_10BIT, ADC_RES_11BIT, ADC_RES_12BIT	}

#### setAdcAveraging
the ADC averaging can be 0 to 7 resulting in no averaging (2^0) or 128 samples averaging (2^7)

#### setShuntGain
the shunt gain can be set to 1/1, 1/2, 1/4 or 1/8 reslting in 320 mV, 160 mV, 80 mV or 40 mV full scale  
{ PG_1,	PG_div_2, PG_div_4, PG_div_8 } 

#### setOperationMode				
ther are 8 different operation modes ranging form inactive over singele conversion to continuous conversion over both voltage and curren. See datatsheet for details.
{MODE_POWER_DOWN, MODE_CURRENT_SINGLE, MODE_VOLTAGE_SINGLE, MODE_ALL_SINGLE, MODE_ADC_OFF, MODE_CURRENT_CONTINUOUS, MODE_VOLTAGE_CONTINUOUS, MODE_ALL_CONTINOUS} 
 
