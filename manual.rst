Red Pitaya STEM 125-14 Reference Manual
========================================
The Red Pitaya board is a comprehensive, ready-to-deploy digital circuit development platform centered around the powerful Xilinx Zynq System on Chip (SoC). This SoC combines the capabilities of a dual-core ARM Cortex A9 processor with the flexibility of an Artix-7 FPGA. With its high-performance FPGA, multifunctional instrument capabilities, and an array of USB, Ethernet, and other ports, the Red Pitaya is equipped to host designs from basic signal processing to advanced digital systems like embedded networks and analyzers.

The board boasts a variety of switches, LEDs, and other I/O devices, enabling a plethora of designs to be realized without the need for supplementary hardware. Additionally, its design supports a wide range of extension modules, ensuring that users can expand their projects using additional modules or custom-designed circuits.


Features
---------

.. table::
    :widths: 10 18

    +------------------------------------+------------------------------------+
    | **Basic**                                                               |
    +====================================+====================================+
    | Processor                          | DUAL CORE ARM CORTEX A9            |
    +------------------------------------+------------------------------------+
    | FPGA                               | FPGA Xilinx Zynq 7010 SOC          |
    +------------------------------------+------------------------------------+
    | RAM                                | 512 MB (4 Gb)                      |
    +------------------------------------+------------------------------------+
    | System memory                      | Micro SD up to 32 GB               |
    +------------------------------------+------------------------------------+
    | Console connection                 | Micro USB                          |
    +------------------------------------+------------------------------------+
    | Power connector                    | Micro USB                          |
    |                                    |                                    |
    +------------------------------------+------------------------------------+
    | Power consumption                  | 5 V, 2 A max                       |
    +------------------------------------+------------------------------------+

.. table::
    :widths: 10 18


    +------------------------------------+------------------------------------+
    | **Connectivity**                                                        |
    +====================================+====================================+
    | Ethernet                           | 1 Gbit                             |
    +------------------------------------+------------------------------------+
    | USB                                | USB 2.0                            |
    +------------------------------------+------------------------------------+
    | WIFI                               | requires WIFI dongle               |
    +------------------------------------+------------------------------------+


.. table::
    :widths: 10 18

    +------------------------------------+------------------------------------+
    | **RF inputs**                                                           |
    +====================================+====================================+
    | RF input channels                  | 2                                  |
    +------------------------------------+------------------------------------+
    | Sample rate                        | 125 MS/s                           |
    +------------------------------------+------------------------------------+
    | ADC resolution                     | 14 bit                             |
    +------------------------------------+------------------------------------+
    | Input impedance                    | 1 MOhm / 10 pF                     |
    +------------------------------------+------------------------------------+
    | Full scale voltage range           | ±1 V (LV) and ±20 V (HV)           |
    +------------------------------------+------------------------------------+
    | Input coupling                     | DC                                 |
    +------------------------------------+------------------------------------+
    | Absolute max. Input voltage range  | 30 V                               |
    |                                    |                                    |
    +------------------------------------+------------------------------------+
    | Input ESD protection               | Yes                                |
    +------------------------------------+------------------------------------+
    | Overload protection                | Protection diodes                  |
    +------------------------------------+------------------------------------+
    | Bandwidth                          | DC - 60 MHz                        |
    +------------------------------------+------------------------------------+


.. table::
    :widths: 10 18

    +------------------------------------+------------------------------------+
    | **RF outputs**                                                          |
    +====================================+====================================+
    | RF output channels                 | 2                                  |
    +------------------------------------+------------------------------------+
    | Sample rate                        | 125 MS/s                           |
    +------------------------------------+------------------------------------+
    | DAC resolution                     | 14 bit                             |
    +------------------------------------+------------------------------------+
    | Load impedance                     | 50 Ohm                             |
    +------------------------------------+------------------------------------+
    | Voltage range                      | ±1 V                               |
    |                                    |                                    |
    +------------------------------------+------------------------------------+
    | Short circut protection            | Yes                                |
    |                                    |                                    |
    +------------------------------------+------------------------------------+
    | Connector type                     | SMA                                |
    +------------------------------------+------------------------------------+
    | Output slew rate                   | 2 V / 10 ns                        |
    +------------------------------------+------------------------------------+
    | Bandwidth                          | DC - 50 MHz                        |
    +------------------------------------+------------------------------------+



.. table::
    :widths: 10 18

    +------------------------------------+------------------------------------+
    | **Extension connector**                                                 | 
    +====================================+====================================+
    | Digital IOs                        | 16                                 |
    +------------------------------------+------------------------------------+
    | Analog inputs                      | 4                                  |
    +------------------------------------+------------------------------------+
    | Analog inputs voltage range        | 0-3.5 V                            |
    +------------------------------------+------------------------------------+
    | Sample rate                        | 100 kS/s                           |
    +------------------------------------+------------------------------------+
    | Resolution                         | 12 bit                             |
    +------------------------------------+------------------------------------+
    | Analog outputs                     | 4                                  |
    +------------------------------------+------------------------------------+
    | Analog outputs voltage range       | 0-1.8 V                            |
    +------------------------------------+------------------------------------+
    | Communication interfaces           | I2C, SPI, UART                     |
    +------------------------------------+------------------------------------+
    | Available voltages                 | +5 V, +3.3 V, -4 V                 |
    +------------------------------------+------------------------------------+
    | external ADC clock                 |  yes                               |
    +------------------------------------+------------------------------------+

.. table::
    :widths: 10 18

    +------------------------------------+------------------------------------+
    | **Synchronisation**                                                     |
    +====================================+====================================+
    | Trigger input                      | Through extension connector        |
    +------------------------------------+------------------------------------+
    | Daisy chain connection             | Over SATA connection               |
    |                                    | (up to 500 Mbps)                   |
    +------------------------------------+------------------------------------+
    | Ref. clock input                   | N/A                                |
    +------------------------------------+------------------------------------+

Advanced On-Board components:
----------------------------

* `ADC <https://www.analog.com/en/products/ltc2145-14.html>`_
* `DAC <https://www.analog.com/en/products/AD9767.html>`_
* `FPGA (Zynq 7010) <https://docs.xilinx.com/v/u/en-US/ds190-Zynq-7000-Overview>`_
* `DC-DC converter <https://www.analog.com/en/products/LTC3615.html>`_
* `Oscillator <https://eu.mouser.com/datasheet/2/417/bf-8746.pdf>`_
* `SRAM-DDR3 <https://www.digikey.com/en/products/detail/micron-technology-inc/MT41J256M16HA-125-E/4315785>`_
* `QSPI <https://www.infineon.com/cms/en/product/memories/nor-flash/standard-spi-nor-flash/quad-spi-flash/s25fl128sagnfi001/>`_

Power
------
To ensure optimal performance and stability of the Red Pitaya, the following power-related items are essential:

-Power Supply: 5 V / 2 A micro USB power supply.

Note:
*It's crucial to power the Red Pitaya boards with the recommended power supply. Using a power supply that provides less power than specified or has very thin power wires can result in abnormal behavior of the device, leading to unexpected reboots and network disconnections. Similarly, powering the Red Pitaya board directly from a PC's USB or a HUB that cannot provide sufficient power, or using a subpar power cable, can also lead to these issues.*

[Image: Red Pitaya Power Schematic]

Powering Red Pitaya through extension connector
--------------------------------------------------------

The Red Pitaya can also be powered through pin 1 of the extension connector :ref:`E2 <E2>`, but in such a case, external protection must be provided by the user in order to protect the board!

.. figure:: Protection.png

Protection circuit between +5 V that is provided over the micro USB power connector and +5 VD that is connected to pin1 of the extension connector :ref:`E2 <E2>`

Analog inputs
--------------
The Red Pitaya board analog front-end features 2 fast analog inputs. 

+---------------------------------+-----------------------------------------------+
| Number of channels              | 2                                             |
+---------------------------------+-----------------------------------------------+
| Sample rate                     | 125 Msps                                      |
+---------------------------------+-----------------------------------------------+
| ADC resolution                  | 14 bits                                       |
+---------------------------------+-----------------------------------------------+
| Input coupling                  | DC                                            |
+---------------------------------+-----------------------------------------------+
| | **Absolute maximum input**    | **30 V (S) (1500 V ESD)**                     |
| | **voltage rating**            |                                               |
+---------------------------------+-----------------------------------------------+
| Overload protection             | protection diodes                             |
|                                 | (under the input voltage rating conditions)   |
+---------------------------------+-----------------------------------------------+
| Connector type                  | SMA                                           |
+---------------------------------+-----------------------------------------------+
| Input stage voltage ranges      | | LV (±1 V)                                   |
|                                 | | HV (±20 V)                                  |
+---------------------------------+-----------------------------------------------+
| Bandwidth                       | 50 MHz (3 dB)                                 |
+---------------------------------+-----------------------------------------------+
    
    .. note::
    
       The overload protection is valid for low-frequency signals. For input signals that contain frequency components beyond 1 kHz, the full-scale value defines the maximum admissible input voltage.
    

    .. note::
    
        The SMA connectors on the cables connected to Red Pitaya must correspond to the standard MIL­C­39012. The central pin must be of suitable length, otherwise, the SMA connector installed in Red Pitaya will mechanically damage the SMA connector.
        The central pin of the SMA connector on Red Pitaya will lose contact with the board and the board will not be possible to repair due to the mechanical damage (separation of the pad from the board).



.. _jumper_pos:


Jumpers
********

Voltage ranges are set by input jumpers, as shown here:

.. figure:: Jumper_settings.png 


Gain can be adjusted independently for both input channels. The adjustment is done by bridging the jumpers located behind the corresponding input SMA connector.
     
.. figure:: Jumper_settings_photo.png
            
    Jumper setting
    
    - The left setting (LV) adjusts to ± 1 V full scale.
    - The right setting (HV) adjusts to ± 20 V full scale.


.. warning::
    
    Jumper settings are limited to the described positions. Any other configuration or use of different jumper types may damage the product and void the warranty.

Input stage schematics
************************


.. figure:: Fast_analog_inputs_sch.png
        
    Fast analog inputs schematics

Analog outputs
---------------

The Red Pitaya board analog front-end features two fast analog outputs.

General Specifications
**********************

+---------------------------------+-----------------------------------------------+
| Number of channels              | 2                                             |
+---------------------------------+-----------------------------------------------+
| Sample rate                     | 125 Msps                                      |
+---------------------------------+-----------------------------------------------+
| DAC resolution                  | 14 bits                                       |
+---------------------------------+-----------------------------------------------+
| Output coupling                 | DC                                            |
+---------------------------------+-----------------------------------------------+
| Load impedance                  | 50 Ω                                          |
+---------------------------------+-----------------------------------------------+
| Full scale power                | > 9 dBm                                       |
+---------------------------------+-----------------------------------------------+
| Connector type                  | SMA                                           |
+---------------------------------+-----------------------------------------------+
| Output slew rate limit          | 200 V/us                                      |
+---------------------------------+-----------------------------------------------+
| Bandwidth                       | 50 MHz (3 dB)                                 |
+---------------------------------+-----------------------------------------------+


.. note::

    The output channels are designed to drive 50 Ω loads. Terminate outputs when channels are not used. Connect a 50 Ω parallel load (SMA Tee junction) in high-impedance load applications.

.. note::

    The typical power level with 1 MHz sine is 9.5 dBm. Output power is subject to slew rate limitations.
    
.. note::

    The SMA connectors on the cables connected to Red Pitaya must correspond to the standard MIL­C­39012. The central pin must be of a suitable length, otherwise, the SMA connector, installed on the Red Pitaya, will mechanically damage the SMA connector. The central pin of the SMA connector on the Red Pitaya will lose contact with the board and the board will not be possible to repair due to the mechanical damage (separation of the pad from the board).
    
.. figure:: Outputs.png
       
    Output channel Output voltage range: ± 1 V
        
The output stage is shown in the picture below.
    
.. figure:: Outputs_stage.png
       
    Output channel schematics
           

Output impedance
************************

The impedance of the output channels (output amplifier and filter) is shown in the figure below.
    
.. figure:: Output_impedance.png
    
    Output impedance


Bandwidth
************************

+---------------------------------+-----------------------------------------------+
| Bandwidth                       | 50 MHz (3 dB)                                 |
+---------------------------------+-----------------------------------------------+

Bandwidth measurements are shown in the picture below. Measurements are taken with the |Agilent MSO7104B| oscilloscope for each frequency step (10 Hz – 60 MHz) of the measured signal. The Red Pitaya board OUT1 is used with 0 dBm output power. The second output channel and both input channels are terminated with 50 Ohm termination. The Oscilloscope ground is used to ground the Red Pitaya board. The oscilloscope input must be set to 50 Ohm input impedance.

.. figure:: Fast_Analog_Outputs_Bandwidt.png


Extension Connector
--------------------
  - Connector: 2 x 26 pins IDC (M) 
    - Power supply: 
    - Available voltages: +5 V, +3.3 V, -3.4 V 
    - Current limitations: 500 mA for +5 V and +3.3 V (to be shared between extension module and USB devices), 50 mA 
      for -3.4 V supply. 

.. _E1:

Extension connector E1
************************

- 3v3 power source
- 16 single ended or 8 differential digital I/Os with 3.3 V logic levels

===  =========== =============== ======================== ==============
Pin  Description FPGA pin number FPGA pin description     Voltage levels
===  =========== =============== ======================== ==============
1    3V3  
2    3V3
3    DIO0_P      G17             IO_L16P_T2_35 (EXT TRIG) 3.3V
4    DIO0_N      G18             IO_L16N_T2_35            3.3V
5    DIO1_P      H16             IO_L13P_T2_MRCC_35       3.3V
6    DIO1_N      H17             IO_L13N_T2_MRCC_35       3.3V
7    DIO2_P      J18             IO_L14P_T2_AD4P_SRCC_35  3.3V
8    DIO2_N      H18             IO_L14N_T2_AD4N_SRCC_35  3.3V
9    DIO3_P      K17             IO_L12P_T1_MRCC_35       3.3V
10   DIO3_N      K18             IO_L12N_T1_MRCC_35       3.3V
11   DIO4_P      L14             IO_L22P_T3_AD7P_35       3.3V
12   DIO4_N      L15             IO_L22N_T3_AD7N_35       3.3V
13   DIO5_P      L16             IO_L11P_T1_SRCC_35       3.3V
14   DIO5_N      L17             IO_L11N_T1_SRCC_35       3.3V
15   DIO6_P      K16             IO_L24P_T3_AD15P_35      3.3V
16   DIO6_N      J16             IO_L24N_T3_AD15N_35      3.3V
17   DIO7_P      M14             IO_L23P_T3_35            3.3V
18   DIO7_N      M15             IO_L23N_T3_35            3.3V
19   NC
20   NC
21   NC
22   NC
23   NC
24   NC
25   GND
26   GND
===  =========== =============== ======================== ==============

All DIOx_y pins are LVCMOS33, with the following abs. max. ratings:
    - min. -0.40V
    - max. 3.3V + 0.55V
    - <8 mA drive strength
    
.. _E2:

Extension connector E2
************************

    - +5 V & -3V4 power source
    - SPI, UART, I2C
    - 4 x slow ADCs
    - 4 x slow DACs
    - Ext. clock for fast ADC
 
.. Table 6: Extension connector E2 pin description

===  ====================== =============== ==================== ==============
Pin  Description            FPGA pin number FPGA pin description Voltage levels
===  ====================== =============== ==================== ==============
1    +5V                                                                         
2    -3.4V (50mA)\ :sup:`1`                                                                         
3    SPI(MOSI)              E9              PS_MIO10_500         3.3V
4    SPI(MISO)              C6              PS_MIO11_500         3.3V
5    SPI(SCK)               D9              PS_MIO12_500         3.3V
6    SPI(CS#)               E8              PS_MIO13_500         3.3V
7    UART(TX)               C8              PS_MIO08             3.3V
8    UART(RX)               C5              PS_MIO09             3.3V
9    I2C(SCL)               B9              PS_MIO50_501         3.3V
10   I2C(SDA)               B13             PS_MIO51_501         3.3V
11   Ext com.mode                                                GND (default)
12   GND                                                       
13   Analog Input 0                                              0-3.5V
14   Analog Input 1                                              0-3.5V
15   Analog Input 2                                              0-3.5V
16   Analog Input 3                                              0-3.5V
17   Analog Output 0                                             0-1.8V
18   Analog Output 1                                             0-1.8V
19   Analog Output 2                                             0-1.8V
20   Analog Output 3                                             0-1.8V
21   GND                                                       
22   GND                                                       
23   Ext Adc CLK+                                                LVDS
24   Ext Adc CLK-                                                LVDS
25   GND                                                       
26   GND                                                       
===  ====================== =============== ==================== ==============

\ :sup:`1` Red Pitaya Version 1.0 has -3.3V on pin 2. Red Pitaya Version 1.1 has -3.4V on pin 2.

Schematics of extension connectors are shown in the picture below.

.. figure:: Extension_connector.png


Software - Advanced Vivado Design Suite Integration for Red Pitaya
-------------------------------------------------------------------
The Red Pitaya seamlessly integrates with Xilinx’s cutting-edge Vivado® Design Suite, optimized for high-performance FPGA development. Vivado brings to the table a plethora of modern tools and design methodologies 

that elevate and streamline contemporary design processes. It boasts enhanced speed, optimizes FPGA resource utilization, and empowers designers to invest their efforts in exploring diverse design possibilities. 
The System Edition is enriched with an embedded logic analyzer, a state-of-the-art high-level synthesis tool, among other avant-garde utilities, ensuring that Red Pitaya-based designs are crafted with precision and efficiency.


