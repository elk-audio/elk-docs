## SENSEI configuration format

# Example Configuration

```
{
    "backends" : [
        {
            "id" : 0,
            "enabled": true,
            "raw_input_enabled": false,
            "type" : "osc",
            "host" : "localhost",
            "port" : 23023,
            "base_path" : "/sensei/sensors",
            "base_raw_input_path" : "/sensei/raw_input"
        }
    ],

    "hw_frontend" : {
        "type" : "raspa_xmos"
    },

    "sensors" : [
        {
            "id" : 5,
            "enabled": true,
            "name" : "test_pin",
            "sensor_type" : "analog_input",
            "mode" : "on_value_changed",
            "adc_resolution" : 8,
            "range" : [0,127],
            "hardware" :
                {
                    "hardware_type" : "analog_input_pin",
                    "pins" : [5],
                    "delta_ticks" : 1,
                    "adc_resolution" : 8
                }
        }
    ]
}
```

# Backends

Backends represent an interface for outputing data on the host or to a network. Currently 2 backend types are implemented, Open Sound Control (osc) and stdout.

  * "id" - Identification number of this backend.
  * "enabled" - Whether this backend will output data or not.
  * "raw input enabled" - If this is true then in addition to the processes value (clipped, scaled and possibly inverted), the unprocessed value from the sensor will also be outputed
  * "type" - The type of backend, currently "osc" is the only recognised value, else will default to stdout.
  * "host" - The address to output osc messages to, accepted values are "localhost" or an ip address. Only valid if type = "osc".
  * "port" - The port used on the above host. Only valid if type = "osc".
  * "base_path" - The Prefix added to osc messages. Only valid if type = "osc".
  * "base_raw_input_path" - The prefix added to osc messages with unprocessed values. Only valid if type = "osc".

# Hardware Frontend

The Hardware frontend is the type and method of connecting to the controller handling the physical sensors. Currently 2 controllers are supported. A Teensy 3.2 over a serial connection and Xmos controller connected through Raspalib.

  * "type" - The type used, recognised values are "raspa_xmos" and "serial_teensy".
  * "port" - The serial device to connect to. Only valid if type = "serial_teensy". 

# Sensor

Sensors are a list of connected hardware sensor or indicators like knobs, buttons, leds, pressure sensors as well as more advanced devices like IMU:s

  * "id" - The identification number of this sensor.
  * "enabled" - If true, values from this sensor will be sent to the output backend(s).
  * "name" - Name of this sensor, will be used if the output backend supports it.
  * "inverted" - If set to true, output values from this sensor will be inverted. For a digital sensor this means true will be outputted as false and for analog sensors
  * "sensor_type" - An abstract type that describes the behaviour of the sensor. Valid 
  * "digital_input" - An input with only 2 possible values, true or false, useful for button or switches.
  * "range_input" - An input with multiple discrete values, i.e. a multi position switch 
  * "analog_input" - An sampled continuous input. different from the range input in that it is intended to be mapped to a continuous range (0 to 1 in the case of the OSC backend) and not discrete values. Could be potentiometers, ribbon controllers. etc.
  * "continuous_input" - Input from a sensor that comes "pre-processed" in floating point format. I.e. IMU:s or similar complex devices.
  * "digital_output" - An output that can be set low or high, for controlling diodes or similar.
  * "range_output" - An output that can accept a range of discrete values.
  * "no_output" - A "helper" sensor that doesn't output anything on it own. For instance for setting up multiplexers
  * "mode" - The transmission mode of the sensor. Currently supported values are:
  * "continuous" - Values will be sent periodically according to the set tick rate.
  * "on_value_changed" - Values will be outputted only if a value is different from the previous value.
  * "range" - The output range of the sensor and can be used to clip the high and low end of an analog or continuous sensor. The values represent the mappings of 0 and 1 respectively of the un-mapped output of the sensor. In the case of an analog input with 8 bits of resolution this is 0 and 255 respectively
  * "timestamp" - If true, all outputs from this sensor will be outputted with a timestamp. If using the OSC backend, then the signature of the output address will have an extra "t" tag at the end, containing the timestamp. 

# Sensor Hardware

The hardware specific config. This will be handled differently depending on what hardware frontend is used, and not all hardware frontend have support for all options.

  * "hardware_type" -  The physical type of sensor used. This will be handled differently depending on the hardware frontend used. Recognised values are:"analog_input_pin, "digital_input_pin", "digital_output_pin", "ribbon", "button", "encoder", "n_way_switch", "stepped_output", "multiplexer", "imu_pitch", "imu_roll", imu_yaw".
  * "pins" - A list of the physical pins the sensor is connected to. The number of pins depend on the sensor hardware type.
  * "adc_resolution" - For analog inputs, this represents the resolution of the analog to digital converter.
  * "delta_ticks" -  The update speed of the output represented as a fraction of the main tick rate of the controller.
  * "multiplexed" - If the sensor is connected through a multiplexer, then this member should be present with the fields below:
  * "multiplexer_id" - The sensor id of the multiplexer the sensor is connected to.
  * "multiplexer_pin" - The pin on the multiplexer to set this sensor active.
  * "polarity" - Sets the polarity of the sensor on a hw level. Reconised values are "active_high" and "active_low".
  * "lowpass_cutoff" -  The cutof frequency in Hz of the filter filtering analog inputs.
  * "lowpass_order" - The steepnes of the above lowpass filter. 
  * "slider_threshold" - Sets the touch on/off threshold. Only applicable if "hardware_type" is "ribbon". 
  * "fast_mode" - If true, the sensor will bypass any filtering or debouncing in order to send a value as soon as possible. Could result in double triggers and/or less accuracy.
