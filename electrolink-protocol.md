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

### pinMode(int[] pin, int pinFnc)

Configures pin to it's specific function input/output or define it as interface.
Pin parameter is always an array. In that way it's possible to put multiple pins in some specific mode with one instruction or to declare pins of some specific interface. 

| IN/OUT              | Params                                              |
|:--------------------|:----------------------------------------------------|
| OUT                 | 0                                                   |
| IN                  | 1                                                   |
| IN_PULL_UP          | 2                                                   |
| IN_PULL_DOWN        | 3                                                   |

Parameters:

`int[] pin` - array of pin numbers
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

### write(int[] pin, int value)
Sets GPIO output high or low in the case where previously pinMode was declared to OUT.

Parameters:

`int[] pin` - array of pin numbers
`int value` - digital output value: 0 - LOW, 1 - HIGH

JSON:
```
{
  "pin": [<pinId0>, <pinId1>, ...],
  "method": "write"
  "params": [<value0>, <value1>]
}
```

### read(int[] pin)
Reads digital state of the GPIO pin in the case where previously pinMode was declared to IN, IN_PULL_UP or IN_PULL_DOWN

Parameters:
`int[] pin` - array of pin numbers

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
### adcRead(int[] pin)

Reads ADC value of the GPIO pin in the case where previously pinMode was declared to ADC

Parameters:
`int[] pin` - array of pin numbers

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

### pwmWrite(int[] pin, int value)

Sets pwm value on the GPIO pin in the case where previously pinMode was declared to PWM.

Parameters:

`int[] pin` - array of pin numbers
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
### spiWrite(int iName, int value)

Sends value to SPI bus. SPI interface needs to be previously set with pinMode function

Parameters:

`int iName` - SPI interface name. This is not pin number, this is name that has been given in pinMode SPI declaration
`int value` - 8bit value 

JSON:
```
{
  "pin": <iName>
  "method": "spiWrite",
  "params": [ <value> ]
}
```

### spiRead(int iName, int nBytes)

Reads values from SPI bus. SPI interface needs to be previously set with pinMode function

Parameters:

`int iName` - SPI interface name. This is not pin number, this is name that has been given in pinMode SPI declaration
`int nBytes` - number of bytes that will be read

JSON:
```
{
  "pin": <iName>
  "method": "spiRead",
  "params": [ <nBytes> ]
}
```
Returns message 

JSON:
```
{
  "pin": <iName>
  "method": "spiRead",
  "value": [<byte0>, <byte1>,...]
}
```