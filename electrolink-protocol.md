## Function List

| SUBSYS              | FNC                                                 |
|:--------------------|:----------------------------------------------------|
| GPIO                | **pinMode**(int pinFnc)                             |
|                     | **write**(int value)                                |
|                     | **read**()                                          |
| ADC                 | **adcRead**()                                       |
| PWM                 | **pwmWrite**(int value)                             |
| SPI                 | **spiWrite**(int value)                             |
|                     | **spiRead**(int nBytes)                             |


## GPIO

### pinMode(int pinFnc)

Configures pin to it's specific function input/output or define it as interface.
Pin parameter is always an array. In that way it's possible to put multiple pins in some specific mode with one instruction or to declare pins of some specific interface. 

| IN/OUT              | Params                                              |
|:--------------------|:----------------------------------------------------|
| OUT                 | 0                                                   |
| IN                  | 1                                                   |
| IN_PULL_UP          | 2                                                   |
| IN_PULL_DOWN        | 3                                                   |

Parameters:

`int pinFnc` - function number

Putting GPIOs in specific interface mode.

| Interface           | Params                              | Pin declaration order | 
|:--------------------|:------------------------------------|:----------------------|
| ADC                 | 4                                   | None                  | 
| PWM                 | 5, FREQUENCY, DUTY_CYCLE            | None                  | 
| SPI                 | 6, NAME, BAUDRATE, POLARITY, PHASE  | MISO, MOSI, SCK       | 
| I2C                 | 7, NAME, FREQUENCY                  | SCL, SDA              | 
| UART                | 8, NAME, BAUDRATE                   | TX, RX                | 

JSON:
```
{
  "pin": [<pinId0>, <pinId1>, ...],
  "method": "pinMode",
  "params": [<pinFnc>]
}
```

### write(int value)
Sets GPIO output high or low in the case where previously pinMode was declared to OUT.

Parameters:

`int value` - digital output value: 0 - LOW, 1 - HIGH

JSON:
```
{
  "pin": [<pinId0>, <pinId1>, ...],
  "method": "write"
  "params": [<value0>, <value1>]
}
```

### read()
Reads digital state of the GPIO pin in the case where previously pinMode was declared to IN, IN_PULL_UP or IN_PULL_DOWN

JSON:
```
{
  "pin": [<pinId>, <pinId>, ...],
  "method": "read"
}
```
Returns message 

JSON:
```
{
  "pin": [<pinId0>, <pinId1>, ...],
  "method": "read",
  "value": [<pinId0_value>, <pinId1_value>,...]
}
```

## Analog (ADC)
### adcRead()

Reads ADC value of the GPIO pin in the case where previously pinMode was declared to ADC

JSON:
```
{
  "pin": [<pinId0>, <pinId1>, ...],
  "method": "adcRead"
}
```
Returns message 

JSON:
```
{
  "pin": [<pinId0>, <pinId1>, ...],
  "method": "adcRead",
  "value": [<pinId0_value>, <pinId1_value>,...]
}
```

## PWM

### pwmWrite(int value)

Sets pwm value on the GPIO pin in the case where previously pinMode was declared to PWM.

Parameters:

`int value` - PWM value

JSON:
```
{
  "pin": [<pinId0>, <pinId1>, ...],
  "method": "pwmWrite",
  "params": [<value0>, <value1>,...]
}
```


## SPI
### spiWrite(int value)

Sends value to SPI bus. SPI interface needs to be previously set with pinMode function

Parameters:

`int NAME` - SPI interface name. This is not pin number, this is name that has been given in pinMode SPI declaration

`int value` - 8bit value 

JSON:
```
{
  "pin": <NAME>
  "method": "spiWrite",
  "params": [ <value> ]
}
```

### spiRead(int nBytes)

Reads values from SPI bus. SPI interface needs to be previously set with pinMode function

Parameters:

`int NAME` - SPI interface name. This is not pin number, this is name that has been given in pinMode SPI declaration

`int nBytes` - number of bytes that will be read

JSON:
```
{
  "pin": <NAME>
  "method": "spiRead",
  "params": [ <nBytes> ]
}
```
Returns message 

JSON:
```
{
  "pin": <NAME>
  "method": "spiRead",
  "value": [<byte0>, <byte1>,...]
}
```

logPinID)

Parameters:

`int analogPinID` - analog pin number (0-7), corresponds to ADC0-ADC7 pins.

Returns message where value is 10 bit ADC read value.

JSON:
```
{
  "method": "analogRead",
  "params": [<analogPinID>]
}
```

### pulseIn(int pinId, int level, int timeout)

Measures pulse duration on a pin. Is compatible with Arduino command pulseIn (http://arduino.cc/en/Reference/PulseIn).

Parameters:

`int pinId` - pin number (0-33)

`int level` - level of the pulse (0 - LOW level, 1 - HIGH)

`int timeout` - number of microseconds to wait until timeout

Returns message pulseIn(int pulseWidth) [09], where the pulseWidth is 0 if no pulse was detected during timeout period or pulse duration in microseconds.

JSON:
```
{
  "method": "pulseIn",
  "params": [<pinId>, <level>, <timeout>]
}
```

## PWM
### pwmStart(int pwmNb, int period)

Enables and configures PWM module. Each PWM module has 3 channels. PWM signal period (and frequency) is the same for all channels in one PWM module, but the signal high time (and duty cycle) can be different. PWM channels that are in the same module are synchronized.

Parameters:

`int pwmNb` - PWM module selection

`int period` - PWM signal period in microseconds. 16 bit value (1-65536) for PWM0 and 32bit value for PWM1.

JSON:
```
{
  "method": "pwmStart",
  "params": [<pwmNb>, <period>]
}
```


### pwmSet(int pwmNb, int channel, int highTime)

Sets the high time of the PWM channel for a selected PWM module.

Parameters:

`int pwmNb` - PWM module selection

`int channel` - PWM channel number (0-2)

`int highTime` - PWM signal high time in microseconds. 16 bit value (0-65535) for PWM0 and 32bit value for PWM1. This value should be less than PWM period or undefined behavior might occur.

JSON:
```
{
  "method": "pwmSet",
  "params": [<pwmNb>, <channlel>, <highTime>]
}
```

### pwmStop(int pwmNb)

Stops the PWM module.

Parameters:

`int pwmNb` - PWM module selction

JSON:
```
{
  "method": "pwmStop",
  "params": [<pwmNb>]
}
```


## SPI
### spiStart(int spiNb, int divider, int mode)

Starts and configures SPI module. This function configure device to master mode. Slave mode is not supported.

Parameters:

`int spiNb` - SPI module selection

`int divider` - SCK signal frequency divider value (1-256). The primary SCK signal frequency is 2MHz.

`int mode` - standart SPI mode number (0-3) which determines clock polarity and phase (http://en.wikipedia.org/wiki/Serial_Peripheral_Interface_Bus#Mode_numbers)

JSON:
```
{
  "method": "spiStart",
  "params": [<spiNb>, <divider>, <mode>]
}
```

### spiTransfer(int spiNb, byte[] data, int rsp)

Executes SPI transaction. User should manually (using GPIO functions) drive slave select line low when executing the transaction. Depending on respond value, the device can send back the data that it received from the slave by sending message `spiTxRx(byte[] slaveData)`.

Parameters:

`int spiNb` - SPI module selection

`byte[] data` - bytes which will be sent to the slave.

`int rsp` - value which tells if device should send back slave data: 0 - do not send, 1 - send back slave data.

JSON:
```
{
  "method": "spiTransfer",
  "params": [<spiNb>, <data>, <rsp>]
}
```

### spiStop(int spiNb)

Disables the SPI module.

`int spiNb` - SPI module selection

JSON:
```
{
  "method": "spiStop",
  "params": [<spiNb>]
}
```

## I2C
### i2cStart()

Initializes I2C module.

JSON:
```
{
  "method": "i2cStart",
  "params": []
}
```

### i2cTransfer(int addr, byte[] wrData, int rdLen)

Executes I2C MASTER WRITE and MASTER READ transactions. The data received during MASTER READ transaction is sent back to the host via `i2cTxRx(int addr, byte[] rcvData)` message. If error occurs during transactions `i2cTxRx(int addr, int errorCode)` is sent back to the host.

Parameters:

`int address` - slave device address.

`byte[] wrData` - byte array which will be sent to the slave during MASTER WRITE. If blob size is 0 MASTER WRITE transaction is skipped.

`int rdLen` - number of bytes to read during MASTER READ transaction. If readLength is 0 MASTER READ transaction is not executed.

JSON:
```
{
  "method": "i2cTransfer",
  "params": [<addr>, <wrData>, <rdLen>]
}
```

### i2cStop()

Disables I2C module.

JSON:
```
{
  "method": "i2cStop",
  "params": []
}
```

## UART
### uartStart(int baudrate)

Initializes UART module with a specified baudrate

Parameters:

`int baudrate` - baudrate of the UART interface.

JSON:
```
{
  "method": "uartStart",
  "params": [<baudrate>]
}
```

### uartSend(byte[] wrData)

Send data to the UART bus

Parameters:

`byte[] rwData` - byte array containing the data to send.

JSON:
```
{
  "method": "uartSend",
  "params": [<wrData>]
}
```

### uartReceive(int rdLen)

Receive data from the UART bus.

Parameters:

`int rdLen` - Number of byte to read. 

JSON:
```
{
  "method": "uartReceive",
  "params": [<rdLen>]
}
```

### uartStop()

Close the UART bus

JSON:
```
{
  "method": "uartStop",
  "params": []
}
```

## System
### registerWrite(int regAddr, int val)

Writes a value directly to the device register located at regAddr address.

`int regAddr` - register address

`int val` - value to write

JSON:
```
{
  "method": "registerWrite",
  "params": [<regAddr>, <val>]
}
```

### registerRead(int regAddr)

Reads a register value. Responds with registerRead(int registerAddress, int registerValue) [101] message.

JSON:
```
{
  "method": "registerRead",
  "params": [<regAddr>]
}
```

### getDeviceInfo()

Returns device information. Responds with getDeviceInfo(int firmwareVersion, byte[] UID, int partNumber, int bootloaderVersion) message.

Return values:

`int firmwareVersion` - [31:24] bits are device (firmware) type and is 0x55 (ASCII 'U') for UPER board, [23:16] bits are firmware major version and [15:0] bits are firmware minor version.

`byte[] UID` - 16 byte long Unique IDentifier, which is read from the device

`int partNumber` - device part number which is read from the device

`int bootloaderVersion` - device bootloader code version which is read from the device

JSON:
```
{
  "method": "getDeviceInfo",
  "params": []
}
```
