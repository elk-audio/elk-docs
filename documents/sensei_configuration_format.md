# Sensei Overview and Configuration Format

Sensei is a hardware abstraction daemon that simplifies the access to the GPIOs
available on the development board, offering OSC endpoints for event reporting
and control (gRPC API is under development and will be available soon).

It allows describing physical devices or controllers connected to an Elk Powered
Board's GPIO through a JSON configuration file. All low level GPIO logic is taken
care by Sensei and it allows programmers to quickly connect, modify and control
physical devices with ease.

The low level GPIO logic or program can either be running on a seperate
micro-controller or the host itself, as is the case with Raspberry Pi systems.
It samples the input GPIO and drives the output GPIO at a periodicity called
**tick rate** which is typically 1000Hz.

Sensei uses the [GPIO Control Protocol](https://github.com/elk-audio/elk-gpio-protocol)
to communicate with the low level GPIO logic program about information such as GPIO configuration, output GPIO values and input GPIO values.

Sensei also generates a .log file similar to Sushi for reporting run-time messages.

## Example Configuration
Lets consider an example Elk Powered board with 8 digital output pins numbered
DO0 - DO7, 8 digital input pins numbered DI0 - DI7 and 3 analog input pins
numbered A0 - A2.

It has the following connections as shown in the example schematic shown below:
  1. An led connected to DO0.
  2. An led level meter connected to DO1 - DO7.
  3. A potentiometer connected to A0.
  4. A button connected to DI0.
  5. An encoder connected to DI1 - DI3.
  6. A 3 way selector switch connected to DI4 - DI6.

![img](./illustrations/sensei_diagrams/example_sensei_schematic.png)

The Sensei configuration for these connections would typically look
like. More examples are availabe
[here](https://github.com/elk-audio/sensei/tree/master/misc/example_configs).
Detailed breakdown and explanations of the various fields are provided in the
following sections.

```
{
    "backends" : [
        {
            "id" : 0,
            "enabled": true,
            "raw_input_enabled": false,
            "type" : "osc",
            "host" : "localhost",
            "port" : 23000,
            "base_path" : "/sensors",
            "base_raw_input_path" : "/raw_input"
        }
    ],

    "hw_frontend" : {
        "type" : "elk_pi"
    },

    "sensors" : [
        {
            "id" : 1,
            "enabled": true,
            "name" : "simple_led",
            "sensor_type" : "digital_output",
            "hardware" :
            {
                "hardware_type" : "digital_output_pin",
                "pins" : [0]
            }
        },
        {
            "id" : 2,
            "enabled": true,
            "name" : "led_level_meter",
            "sensor_type" : "analog_output",
            "range" : [0, 7],
            "hardware" :
            {
                "hardware_type" : "stepped_output",
                "pins" : [1,2,3,4,5,6,7]
            }
        },
        {
            "id" : 3,
            "enabled": true,
            "name" : "button",
            "sensor_type" : "digital_input",
            "inverted" : "true",
            "mode" : "on_value_changed",
            "hardware" :
            {
                "hardware_type" : "digital_input_pin",
                "polarity" : "active_low",
                "pins" : [0],
                "delta_ticks" : 1
            }
        },
        {
            "id" : 4,
            "enabled": true,
            "name" : "rotary_encoder",
            "sensor_type" : "analog_input",
            "mode" : "on_value_changed",
            "range" : [0, 15],
            "hardware" :
            {
                "hardware_type" : "encoder",
                "polarity" : "active_low",
                "pins" : [1, 2],
                "delta_ticks" : 1
            }
        },
        {
            "id" : 5,
            "enabled": true,
            "name" : "rotary_encoder_button",
            "sensor_type" : "digital_input",
            "inverted" : "true",
            "mode" : "on_value_changed",
            "hardware" :
            {
                "hardware_type" : "digital_input_pin",
                "polarity" : "active_low",
                "pins" : [3],
                "delta_ticks" : 1
            }
        },
        {
            "id" : 6,
            "enabled": true,
            "name" : "three_way_switch",
            "sensor_type" : "range_input",
            "mode" : "on_value_changed",
            "inverted" : true,
            "range" : [1, 3],
            "hardware" :
            {
                "hardware_type" : "n_way_switch",
                "pins" : [4,5,6],
                "delta_ticks" : 1
            }
          },
        {
            "id" : 7,
            "enabled": true,
            "name" : "potentiometer",
            "sensor_type" : "analog_input",
            "mode" : "on_value_changed",
            "hardware" :
            {
                "hardware_type" : "analog_input_pin",
                "pins" : [0],
                "delta_ticks" : 1,
                "adc_resolution" : 8,
                "filter_time_constant" : 0.020
            }
        }
    ]
}
```

## Backends

The Sensei backends represent an interface for outputing data on the host or to
a network. Currently 2 backend types are implemented which are Open Sound
Control (osc) and stdout.

Backends are described using a `backends` json field, which is an array where
you can define multiple backends. Each backend's subfields are described below.

  * `id` - Identification number of this backend.
  * `enabled` - Whether this backend will output data or not.
  * `raw_input_enabled` - If this is true then in addition to the processes
     value (clipped, scaled and possibly inverted), the unprocessed value from
     the sensor
     will also be output.
  * `type` - The type of backend, currently `osc` is the only recognised value,
  else will default to stdout.
  * `host` - The address to output osc messages to, accepted values are
    `localhost` or an ip address. Only valid if type is `osc`.
  * `port` - The port used on the above host. Only valid if type is `osc`.
  * `base_path` - The Prefix added to osc messages. Only valid if type is `osc`.
  * `base_raw_input_path` - The prefix added to osc messages with unprocessed
     values. Only valid if type is `osc`.

## Hardware Frontend

The Hardware frontend is the type and method of connecting to the program which handles the low level logic of the physical devices connected to the pins. The
`hw_frontend` field has only one subfield which describes its type.

  * `type` - The hw frontend type used, Currently, only two types of hardware
    frontend is supported:
    1. `raspa_gpio` - for devices where a micro-controller is handling the GPIO
        logic. Sensei communicates with the micro-controller using
        [RASPA](https://github.com/elk-audio/raspa).
    2. `elk_pi` - for Elk Pi devices, where Sensei itself takes care of the
        GPIO logic.

## Sensors/Controllers

Sensei needs information about the sensor or physical device is connected to the
pins and how it should handle data to/from those sensors. It uses this
information and informs the hardware frontend about the various physical
devices and the pins to which they are connected to.

For this reason, the Sensei json config file should contain the definition of:
  1. A description of how Sensei should process the raw data from these sensors.
  2. A description of the physical sensor and the pin(s) to which it is attached.

All sensors are defined in a `sensors` array field in the json file. Each entry
describes how Sensei will handle that sensor and contains a `hardware` subfield
which describes what physical device/sensor is connected.

### Handling of sensor data

Sensei uses an abstract representation of all physical sensors which can fall
into any of the following categories. All sensor values are represented in
floating point number, whose range depends on these categories.

  1. `digital_input` - An input with only 2 possible values, on or off, useful
      for **single pin** button or switches. The sensor can have values 0.0 or 1.0.
  2. `range_input` - An input with multiple discrete values, i.e. a multi
      position switch or an LED array. Its range has to be specified
      explicitly using the `range` keyword.
  3. `continuous_input` - An Input from a sensor that comes "pre-processed" in
      floating point format.
  4. `analog_input` - An sampled continuous input that is different from the
      range input in that it is intended to be mapped to a continuous range
      (0.0 to 1.0 in the case of the OSC backend) and not discrete values. This
      also needs the `range` keyword to be explicitly defined so that Sensei
      can normalize the sensor values. Could be potentiometers, encoders etc.
  5. `digital_output` - An output that can be set on of off, like an LED.
      The sensor can have values 0.0 or 1.0.
  6. `range_output` - An output that can accept a range of discrete values. Its
      range has to be specified explicitly using the `range` keyword.
  7. `analog_output` - Similar to `analog_input`, this can be used to represent
      an output controller whose values range from 0.0 to 1.0. The `range` keyword
      needs to be explicitly defined for this type of sensor.
  8. `no_output` - A "helper" sensor that doesn't output anything on it own but
      controls other sensors. Look at the multiplexer section described below.

The first part of the `sensors` json field is a description of how Sensei will
handle the sensor and its data. It contains the following fields:

  * `id` - A unique identification number of the sensor. If an `id` is duplicated,
     Sensei will log an error message stating the same.
  * `enabled` - Can be `true` or `false`. If true, values from this sensor will
     be sent to the output backend(s).
  * `name` - Name of this sensor, will be used if the output backend supports it.
  * `sensor_type` - Any of the sensor types as described above.
  * `inverted` - Can be `true` or `false`. If true, Sensei will invert the values.
    for both output and input controllers.
  * `mode` - Specifies the notification mode of an input sensor/controller.
     Currently supported values are:
    1. `on_value_changed` - Values will be outputted only if a change in the sensor
      state has been detected. This is the default mode.
    2. `continuous` - Values will be sent periodically according to the set tick rate.
  * `range` - This represents the range of the sensor. The following `sensor_type`
     require this explicit definition:
    * `range_input` and `range_output` : Values of the sensor/controller range from minimum value to maximum. In the example json config shown above, the three
    way switch will have values 1.0, 2.0 and 3.0.
    * `analog_input` and `analog_output` : Values of the sensor/controller are
    normalized between minumam and maximum range. In the example json config
    shown above, the rotary encoder has a range of 15 and so it has 15
    possible values between 0.0 to 1.0.
  * `timestamp` - If true, all outputs from this sensor will be outputted
     with a timestamp. If using the OSC backend, then the signature of the
     output address will have an extra "t" tag at the end, containing the
     timestamp.

## Supported Sensor/Controller Hardware

The second part of the `sensors` json field is a description of the actual
sensor/controller and the pins to which they are connected to. There are three
major categories of controllers:

  1. **Digital Outputs** : LEDs, Relays etc. Connected to the Digital Output pins
     of the board.
  2. **Digital Inputs** : Buttons, switches, rotary encoders etc. Connected to the
     Digital Input pins of the board.
  3. **Analog Inputs** : Pots, ribbon cables etc. Connected to the Analog Input
  pins of the board. All analog input's

The `hardware` section of each `sensors` entry in the json config describes the
sensor/controller type. The following list contains the required subfields.
  * `hardware_type` : the type of sensor/controller. The following sections
     explain this in greater detail.

     The following `hardware_type` represent sensors/controllers connected to
     the Digital Output Pins:
      * `digital_output_pin`
      * `stepped_output`
      * `multiplexer`

      The following `hardware_type` represent sensors/controllers connected to
     the Digital Input Pins:
      * `digital_input_pin`
      * `n_way_switch`
      * `encoder`

      The following `hardware_type` represent sensors/controllers connected to
      the Analog Pins:
      * `analog_input_pin`
  * `pins` : An array of the pin numbers used by the controllers.
    *  Sensei automatically figures out their pin type (Digital Output, Digital
       Input or Analog Input) based on the `hardware_type` and so it is not
       necessary to mention the prefix of the pin number (DO, DI, A) here.
    * The pins can be completely random and do not have to be contiguous.
    * The order of the pins is very important and will affect the behavior. Make
      sure that the order follows the intention of use.

Optionally, it can also contain the following subfields depending on the
`hardware_type`:
  * `polarity` : Optional field to describe the polarity of the sensor/controller.
    By default all controllers are active high. It can take values `active_high`
    or `active_low`. This is not applicable to `analog_input_pin` hardware type.
  * `delta_ticks` : This field allows you to donwsample the sensor/controller.
    This is specified in terms of system tick rate. By default, this is set to 1
    for all sensors. For example, when delta_ticks is 4 for an input sensor, the
    sensor value is read only once every 4 system ticks. This can be used to
    handle noisy sensors. Not applicable to `multiplexer` hardware type.
  * `adc_resolution` : Applicable only to `analog_input_pin` hardware types, this
     sets the adc resoultion for a particular sensor. By default, it is at max
     resoultion of the on board adc. This is particularly usefull to reduce
     crosstalk.
  * `filter_time_constant`: Applicable only to `analog_input_pin` hardware types,
     this sets the settling time constant for a particular sensor's digital filter.
     By default, the settling time is 20ms.
  * `fast_mode` : By default, all digital input pins go through software debouncing
    to prevent erraneous values. By setting this to `true`, the debouncing for
    a particular sensor is switched off. You can use this if you have hardware
    debouncing filters setup.