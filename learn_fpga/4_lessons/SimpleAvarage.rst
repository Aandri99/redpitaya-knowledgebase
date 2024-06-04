############################
Moving average on Red Pitaya
############################

On the Red Pitaya development board, we will build a signal scaling and filtering circuit with a small averaging filter that calculates the average of four consecutive samples.

=========================
Creation of a new project
=========================

1) Create a new folder called "Simple_Moving_Average" in **/RedPitaya-FPGA/prj/Examples**.
2) Copy all files from **/RedPitaya-FPGA/prj/v0.94** into the newly created folder.
3) Create a new file named **red_pitaya_proc.vhd** in the "Simple_Moving_Average/rtl" directory.
4) Copy the framework of filter development into the file.

.. code-block:: vhdl

    library IEEE;
    use IEEE.STD_LOGIC_1164.all;
    use IEEE.NUMERIC_STD.all;
    
    entity red_pitaya_proc is
    port (
        clk_i       : in  std_logic;                      -- bus clock
        rstn_i      : in  std_logic;                      -- bus reset - active low
        
        sys_addr    : in  std_logic_vector(31 downto 0);  -- bus address
        sys_wdata   : in  std_logic_vector(31 downto 0);  -- bus write data
        sys_wen     : in  std_logic;                      -- bus write enable
        sys_ren     : in  std_logic;                      -- bus read enable
        sys_rdata   : out std_logic_vector(31 downto 0);  -- bus read data
        sys_err     : out std_logic;                      -- bus error indicator
        sys_ack     : out std_logic;                      -- bus acknowledge signal
    
        adc_i : in  std_logic_vector(13 downto 0);
        adc_o : out std_logic_vector(13 downto 0)
        );
    end red_pitaya_proc;
    
    architecture Behavioral of red_pitaya_proc is
        component moving_average
            port (
                data_i   : in std_logic_vector (13 downto 0);
                clk_i    : in std_logic;
                rstn_i   : in std_logic;
                tag_i    : in unsigned (1 downto 0);
                data_o   : out std_logic_vector (13 downto 0));
        end component;
    
        constant ZERO       : std_logic_vector(31 downto 0) := (others => '0');         -- Padding registers
        signal tag_i: unsigned(1 downto 0) := "01";
    
    
    begin
    
    rp_average: moving_average
            port map (
                data_i => adc_i,
                clk_i => clk_i,
                rstn_i => rstn_i,
                tag_i => tag_i,
                data_o => adc_o
            );
    
    pbusr: process(clk_i)
    begin
        if(rising_edge(clk_i)) then
            if (rstn_i = '0') then
                sys_ack <= '0';
                tag_i <= "01";
            else
                sys_ack <= sys_wen or sys_ren;  -- acknowledge transactions
                if sys_wen='1' then
                    if sys_addr(19 downto 0) = X"00008" then
                        tag_i <= unsigned(sys_wdata(1 downto 0));
                    end if;
                end if;
            end if;
        end if;
    end process;
    
    -- System error
    sys_err <= '0';
    
    -- Decode address & read data
    with sys_addr(19 downto 0) select
        sys_rdata <=    X"FEEDBACC" when (X"00050"),  -- ID
                        ZERO(31 downto 2) & std_logic_vector(tag_i) when (X"00008"),
                        X"00000000" when others;
    
    end Behavioral;

Also, copy **red_pitaya_scope.v** from **/RedPitaya-FPGA/rtl/** to **Simple_Moving_Average/rtl** and rename it to *loop_scope.v*. Also, change the name of the module inside the file from *red_pitaya_scope* to *loop_scope*.

Now create **red_pitaya_proc_tb.vhd** in **/Simple_Moving_Average/tbn** and copy the code there:

.. code-block:: vhdl

    library IEEE;
    use IEEE.STD_LOGIC_1164.all;
    use IEEE.numeric_std.all;
    
    entity red_pitaya_proc_tb is
    end red_pitaya_proc_tb;
    
    architecture Behavioral of red_pitaya_proc_tb is
    
        component red_pitaya_proc
            port (
                clk_i   : in  std_logic;
                rstn_i  : in  std_logic;
    
                sys_addr    : in  std_logic_vector(31 downto 0);  -- bus address
                sys_wdata   : in  std_logic_vector(31 downto 0);  -- bus write data
                sys_wen     : in  std_logic;                      -- bus write enable
                sys_ren     : in  std_logic;                      -- bus read enable
                sys_rdata   : out std_logic_vector(31 downto 0);  -- bus read data
                sys_err     : out std_logic;                      -- bus error indicator
                sys_ack     : out std_logic;                      -- bus acknowledge signal
    
                adc_i   : in  std_logic_vector(13 downto 0);
                adc_o   : out std_logic_vector(13 downto 0)
            );
        end component;
    
        signal clk_i   : std_logic := '0';
        signal rstn_i  : std_logic;
        signal addr_i  : std_logic_vector(31 downto 0);
        signal wdata_i : std_logic_vector(31 downto 0);
        signal wen_i   : std_logic;
        signal ren_i   : std_logic;
        signal rdata_o : std_logic_vector(31 downto 0);
        signal err_o   : std_logic;
        signal ack_o   : std_logic;
    
        signal adc_i   : std_logic_vector(13 downto 0);
        signal adc_o   : std_logic_vector(13 downto 0);
    
        signal i : integer range 0 to 30 := 0;
        type memory_type is array (0 to 29) of integer range -128 to 127;
        signal sine : memory_type := (0, 16, 31, 45, 58, 67, 74, 77, 77, 74, 67, 58, 45, 31, 16, 0,
                                        -16, -31, -45, -58, -67, -74, -77, -77, -74, -67, -58, -45, -31, -16);
    
        -- Simulation control
        signal sim : std_logic := '0';
    
        constant T  : time := 8 ns;
    
    begin
        uut : red_pitaya_proc port map (
                    clk_i       => clk_i,
                    rstn_i      => rstn_i,
                    sys_addr    => addr_i,
                    sys_wdata   => wdata_i,
                    sys_wen     => wen_i,
                    sys_ren     => ren_i,
                    sys_rdata   => rdata_o,
                    sys_err     => err_o,
                    sys_ack     => ack_o,
                    adc_i       => adc_i,
                    adc_o       => adc_o
                );
    
        -- Define the clock
        clk_process : process
        begin
            if sim = '0' then
                clk_i <= '0';
                wait for T/2;
                clk_i <= '1';
                wait for T/2;
            else
                wait;
            end if;
        end process;
    
        -- Generate a sine signal from the table
        singen : process(clk_i)
        begin
            if (rising_edge(clk_i)) then
    --            adc_i <= std_logic_vector(to_signed(20*sine(i), 14));
                if (sine(i) > 0) then
                    adc_i <= std_logic_vector(to_signed(2000, 14));
                else
                    adc_i <= std_logic_vector(to_signed(-2000, 14));
                end if;
                i <= i + 1;
                if (i = 29) then
                    i <= 0;
                end if;
            end if;
        end process;
    
        -- Sets the simplified AXI bus signals
        stim_proc : process
        begin
            rstn_i  <= '0';                 -- active reset
            addr_i  <= X"00000000";
            wdata_i <= X"00000000";
            wen_i   <= '0'; ren_i <= '0';
            wait for 10*T;
    
            rstn_i  <= '1';
            addr_i  <= x"00000050";
    	    ren_i   <= '1'; wait for T;
            ren_i   <= '0'; wait for T;
            wait for 10*T;
    
            rstn_i  <= '1';                 -- deactivate reset, write to register
            addr_i  <= X"00000008";
            wdata_i <= X"00000002";
            wen_i   <= '1'; wait for T;
            wen_i   <= '0'; wait for T;
    
            wait for 100*T;                 -- entry of a new value in the register
            wdata_i <= x"00000003";
            wen_i   <= '1';
    
            wait for T;
            addr_i  <= X"00000000";
            wen_i <= '0';
    
            wait for 10000*T;
            sim <= '1';                     -- stop the simulation
            wait;
        end process;
    
    end;

Now we need to create a project generation script. Make a copy of the **red_pitaya_vivado_project_Z10.tcl** and name it **Average_project.tcl**, for example.

We need to change some strings in the file:

.. code-block:: tcl
    :force:

    cd prj/$prj_name 			→ cd prj/Examples/$prj_name
    set path_brd ./../brd 		→ set path_brd ./../../brd
    set path_sdc ../../sdc 		→ set path_sdc ../../../sdc
    add_files  ../../$path_rtl 	→ add_files  ../../../$path_rtl

Add a variable:

.. code-block:: tcl
    :force:

    set path_tbn tbn

Also, we need to add the following strings after the string in the second code-block below:

.. code-block:: tcl
    :force:

    add_files -fileset sim_1 -norecurse $path_tbn/red_pitaya_proc_tb.vhd

.. code-block:: tcl
    :force:

    add_files $path_bd

Now we can generate a project (the *-tclargs* parameter should be the same as the main project folder name):

.. code-block:: shell-session

    vivado -source Average_project.tcl -tclargs "Simple_Moving_Average"

We can test if everything is working OK, by running a **dummy Generate Bitstream** command. If everything is working correctly, the bitstream should generate without any issues.

Edit the file **red_pitaya_top.sv**. Declare two new signals (*adc_i* and *adc_o*), connect them to the oscilloscope module, and replace *red_pitaya_scope* with our new *loop_scope*. The oscilloscope component connection code should be located around line 502:

.. code-block:: verilog

    ////////////////////////////////////////////////////////////////////////////////
    // oscilloscope
    ////////////////////////////////////////////////////////////////////////////////

    logic trig_asg_out;
    logic  [14-1: 0] adc_i;
    logic  [14-1: 0] adc_o;

    loop_scope i_scope (
        // Simple Moving Average
        .adc_in        (adc_o       ),
        .adc_out       (adc_i       ),
        // ADC
        .adc_a_i       (adc_dat[0]  ),  // CH 1
        .adc_b_i       (adc_dat[1]  ),  // CH 2
        ...

Add two ADC data ports to the **loop_scope.v** file (found under *Design Sources* by expanding the *red_pitaya_top* module and double clicking *i_scope: loop_scope*).

.. code-block:: verilog

    module loop_scope #(parameter RSZ = 14  // RAM size 2^RSZ
    )(
        // Simple Moving Average
        input      [ 14-1: 0] adc_in          ,  
        output     [ 14-1: 0] adc_out         ,
        // ADC
        input                 adc_clk_i       ,  // ADC clock
        input                 adc_rstn_i      ,  // ADC reset - active low



Furthermore, replace this process:

.. code-block:: verilog

    always @(posedge adc_clk_i) begin
        if (adc_we && adc_dv_del) begin
            adc_a_buf[adc_wp] <= adc_a_bram_in ;
            adc_b_buf[adc_wp] <= adc_b_bram_in ;
        end
    end

With this one:

.. code-block:: verilog

    // Simple Moving Average
    always @(posedge adc_clk_i) begin
        if (adc_we && adc_dv) begin
            adc_a_buf[adc_wp] <= adc_in ;
            adc_b_buf[adc_wp] <= adc_b_bram_in;
        end
    end
    
    assign adc_out = adc_b_bram_in;


Then, in the file **red_pitaya_top.sv**, we must connect the signals to **red_pitaya_proc**. Add the following code at the end of the module (around **line 630**):

.. code-block:: verilog

    ///////////////////////////////////////////
    // Simple Moving Average
    ///////////////////////////////////////////
    
    red_pitaya_proc i_proc (
        .clk_i    (  adc_clk     ),  // clock
        .rstn_i   (  adc_rstn    ),  // reset - active low
        .sys_addr   (  sys[6].addr ),  // address
        .sys_wdata  (  sys[6].wdata),  // write data
        .sys_wen    (  sys[6].wen  ),  // write enable
        .sys_ren    (  sys[6].ren  ),  // read enable
        .sys_rdata  (  sys[6].rdata),  // read data
        .sys_err    (  sys[6].err  ),  // error indicator
        .sys_ack    (  sys[6].ack  ),  // acknowledge signal
        .adc_i    (  adc_i       ),
        .adc_o    (  adc_o       )
    );

We need to remove the stub for the current bus (near **line 330 - change the i=6 to i=7):

.. code-block:: vhdl

    generate
        for (genvar i=7; i<8; i++) begin: for_sys
            sys_bus_stub sys_bus_stub_5_7 (sys[i]);
        end: for_sys
    endgenerate

After these manipulations, we have redirected the data from the **red_pitaya_proc.vhd** module to the first ADC channel. And the data from the second channel was connected to the **red_pitaya_proc.vhd** input. Within this module, you can already start processing data.


=================================
Development of the moving average
=================================

Create a scheme that calculates the current average of the last three inputs. Basic outline of the moving average:

.. figure:: img/MovingAvg1.png
    :width: 600
    :align: center

Connections:

    * clk, reset (active at logical 0)
    * data_i, 14-bit input
    * tag_i, 2-bit control input
    * data_o, 14-bit output
    * tag_o, 2-bit control output

The data comes into the circuit one after the other, and the control input indicates the cycles in which the data is valid. At *tag_i = 01*, the first data row is at the input; at *10*, the second data row is at the output; and at *11*, the last data row is at the output:

.. table::
    :align: center

    +-------+------+------+------+------+------+------+------+------+------+
    | cycle | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    |
    +=======+======+======+======+======+======+======+======+======+======+
    | tag_i | 00   | 01   | 10   | 10   | 10   | 10   | 11   | 00   | 00   |
    +-------+------+------+------+------+------+------+------+------+------+
    | data_i| xx   | 100  | 50   | 200  | 200  | 200  | 120  | xx   | xx   |
    +-------+------+------+------+------+------+------+------+------+------+

The task of the circuit is to calculate the current average of the last three values. For the first valid data, assume that the previous two values are equal to 0.

The circuit contains three series-connected registers and a combinational circuit for calculating the average value after equations:
:math:`p = (a + b + c) * 1/3 ≈ ((a + b + c) * 85) >> 8`

Instead of dividing by 3, we will use an approximation: ⅓ ≈ 85/256. Use a 7-bit constant of 85 to multiply. Division by 256 represents the value shifted by 8 places to the right. The shift is made by selection subvector, where the lower 8 bits of the product are removed.

In order to implement this, we will create a new component with VHDL:

Create a new file **moving_average.vhd** in **Simple_Moving_Average/rtl** (*Add Sources => Add or create design sources => Create File (VHDL)*).

----------------
Code Explanation
----------------

Define inputs and outputs:

.. code-block:: vhdl

    entity moving_average is
    Port ( data_i   : in std_logic_vector (13 downto 0);    -- adc input data
           clk_i    : in std_logic;                         -- bus clock 
           rstn_i   : in std_logic;                         -- bus reset - active low
           tag_i    : in unsigned (1 downto 0);             -- filter window size
           data_o   : out std_logic_vector (13 downto 0));  -- filtered data
    end moving_average;
 

We will need some memory to store previous values. Describe the memory type and create it. Also, we will need some kind of register to store the sum:

.. code-block:: vhdl

    architecture Behavioral of moving_average is
        type mem_t is array (0 to 2) of signed (13 downto 0);
    
        signal regs: mem_t := (others => (others => '0')); -- buffer for moving average algorithm
        signal sum: signed(13 downto 0); -- register for storing the sum of register values
    begin


The data is updated for each clk, thus, the process runs at each clock change:

.. code-block:: vhdl

    process (clk_i)
    begin
        if(rising_edge(clk_i)) then

We need to reset the registers:

.. code-block:: vhdl

    if (rstn_i = '0') then
        sum <= "00000000000000";


Connect the first register with the ADC directly.

.. code-block:: vhdl

    regs(0) <= signed(data_i);


The summer will always constructively add 3 registers:

.. code-block:: vhdl

    sum <= regs(0) + regs(1) + regs(2);


Then we should describe connections among registers. We should keep in mind that the summer constructively adds 3 registers. Thus, we need to reset register values to 0 so that the moving average is calculated correctly each time. 

.. code-block:: vhdl

    if (tag_i(1) = '1') then
        regs(1) <= regs(0);
    else
        regs(1) <= "00000000000000";
    end if;
            
    if (tag_i(0) = '1') then
        regs(2) <= regs(1);
    else
        regs(2) <= "00000000000000";
    end if;

The last thing we need is the multiplexer to calculate an average value for a buffer with different lengths. Since division is a pretty complex procedure, we need to simplify it. One of the approaches is a real number with a fixed point. We can represent a division as 1/3 ≈ 85/256. Division by 256 is executed by a simple operation of right logical shift.

.. code-block:: vhdl

    case tag_i is
        -- regs
        when "01" => data_o <= std_logic_vector(sum);
                
        -- regs / 2
        when "10" => data_o <= std_logic_vector(shift_right(sum, 1));
                
        -- (regs * 85) / 256
        when "11" => data_o <= std_logic_vector(resize(shift_right(sum * 85, 8), 14));
                     
        -- (regs * 85) / 256
        when others => data_o <= std_logic_vector(resize(shift_right(sum * 85, 8), 14));
    end case;

-----------------
The complete code
-----------------

.. code-block:: vhdl

    library IEEE;
    use IEEE.STD_LOGIC_1164.ALL;
    use IEEE.NUMERIC_STD.all;
    
    entity moving_average is
        Port ( data_i   : in std_logic_vector (13 downto 0);    --
               clk_i    : in std_logic;                         -- bus clock
               rstn_i   : in std_logic;                         -- bus reset - active low
               tag_i    : in unsigned (1 downto 0);             --
               data_o   : out std_logic_vector (13 downto 0));  --
    end moving_average;
    
    architecture Behavioral of moving_average is
        type mem_t is array (0 to 2) of signed (13 downto 0);
    
        signal regs: mem_t := (others => (others => '0')); -- buffer for moving average algorithm
        signal sum: signed(13 downto 0);
    begin
    
    regs(0) <= signed(data_i);
    
    process (clk_i)
    begin
        if(rising_edge(clk_i)) then
            if (rstn_i = '0') then
                sum <= "00000000000000";
            else
                case tag_i is
                    -- regs
                    when "01" => data_o <= std_logic_vector(sum);
    
                    -- regs / 2
                    when "10" => data_o <= std_logic_vector(shift_right(sum, 1));
    
                    -- (regs * 85) / 256
                    when "11" => data_o <= std_logic_vector(resize(shift_right(sum * 85, 8), 14));
    
                    -- (regs * 85) / 256
                    when others => data_o <= std_logic_vector(resize(shift_right(sum * 85, 8), 14));
                end case;
    
                if (tag_i(1) = '1') then
                    regs(1) <= regs(0);
                else
                    regs(1) <= "00000000000000";
                end if;
    
                if (tag_i(0) = '1') then
                    regs(2) <= regs(1);
                else
                    regs(2) <= "00000000000000";
                end if;
    
                sum <= regs(0) + regs(1) + regs(2);
            end if;
        end if;
    end process;
    
    end Behavioral;

By clicking the **+** sign under sources, you can add the **red_pitaya_proc.vhd** file to the project. The previously created module/component must be added to **red_pitaya_proc**. The component *moving_average* is already added to the file (*component ... end component*), so we just add the component connection to the architecture (anywhere between the *begin* and *end architecture* lines):

.. code-block:: vhdl

    rp_average: 
        moving_average 
            port map (
                data_i => adc_i,
                clk_i => clk_i,
                rstn_i => rstn_i,
                tag_i => tag_i,
                data_o => adc_o
            );


Create a register/signal in the architecture to store the moving average of a chosen length- Between the *end component* and the *begin* lines, insert the following code:

.. code-block:: vhdl

    signal tag_i: unsigned(1 downto 0) := "01";

Define the value after the reset in the *process*: 

.. code-block:: vhdl

    if (rstn_i = '0') then
        tag_i <= "01";
    else
    ...


====================
Work with registers
====================

In order to change the buffer dimension, we need to have the "writing" rights for this register at the address. The module **red_pitaya_proc** is already connected to the system bus and has the following address: 0x406xxxxx. Upon receiving data by address, we must write in the *tag_i* register. We add the modification at the end of the **red_pitaya_proc.vhd** file:


.. code-block:: vhdl

    -- Decode address & read data
    with sys_addr(19 downto 0) select
        sys_rdata <=    X"FE240000" when (X"00050"),  -- ID
                        ZERO(31 downto 2) & std_logic_vector(tag_i) when (X"00008"),
                        X"00000000" when others;

You can find more details about the Red Pitaya register map |register map|.

.. |register map| raw:: html

    <a href="https://redpitaya.readthedocs.io/en/latest/developerGuide/software/build/fpga/fpga.html#registers" target="_blank">here</a>

Device enquiry and their configuration are made by 0x40600000, thus, we’re using 0x40600008.

==========
Simulation
==========

**red_pitaya_proc_tb.vhd** should be defined as the upper module in the *Simulation Sources-> sim_1*:

.. figure:: img/MovingAvg2.png
    :width: 600
    :align: center

Launch the simulation and configure the signals adc_i and adc_o as analog:

.. figure:: img/MovingAvg3.png
    :width: 600
    :align: center

Setup the data type of signal:

.. figure:: img/MovingAvg4.png
    :width: 600
    :align: center

Setup the display of these signals:

.. figure:: img/MovingAvg5.png
    :width: 400
    :align: center

.. figure:: img/MovingAvg6.png
    :width: 500
    :align: center
    
Set the simulation time to 10 us and restart the simulation:

.. figure:: img/MovingAvg11.png
    :width: 600
    :align: center

After the simulation is done, you should see the following oscillogram:

.. figure:: img/MovingAvg7.png
    :width: 700
    :align: center

We can notice that the signal gets corrupted when we change the size of tag_i (about 5 us on the oscillogram). This is caused by the fact that when we increase the size of tag_i, one or two registers become empty and the signal amplitude falls down.

To see how this filter handles a sinewave, comment the rectangle generation and uncomment the sine generation in the **red_pitaya_proc_tb.vhd** file (located in *Simulation Sources*):

.. code-block:: vhdl

    -- Generate a sine signal from the table
    singen : process(clk_i)
    begin
        if(rising_edge(clk_i)) then
            adc_i <= std_logic_vector(to_signed(20*sine(i), 14));
    --        if (sine(i) > 0) then
    --          adc_i <= std_logic_vector(to_signed(2000, 14));
    --        else
    --          adc_i <= std_logic_vector(to_signed(-2000, 14));
    --        end if;
            i <= i+ 1;
            if(i = 29) then
                i <= 0;
            end if;
        end if;
    end process;


==============================
Upload bitstream to Red Pitaya
==============================

Insert an SD card with the uploaded ecosystem.

.. tabs::

    .. tab:: OS version 1.04 or older

        Please note that you need to change the forward slashes to backward slashes on Windows.

        1. Open Terminal or CMD and go to the .bit file location.

        .. code-block:: bash
    
            cd <Path/to/RedPitaya/repository>/prj/Examples/Simple_moving_average/tmp/Simple_moving_average/Simple_moving_average.runs/impl_1

        2. Send the .bit file to the Red Pitaya with the ``scp`` command or use WinSCP or a similar tool to perform the operation.

        .. code-block:: bash

            scp system_wrapper.bit root@rp-xxxxxx.local:/root/Simple_moving_average.bit

        3. Now establish an SSH communication with your Red Pitaya and check if you have the copy *Simple_moving_average.bit* in the root directory.

        .. code-block:: bash

            redpitaya> ls

        4. Load the *Simple_moving_average.bit* to **xdevcfg** with

        .. code-block:: bash

            redpitaya> cat Simple_moving_average.bit > /dev/xdevcfg

    .. tab:: OS version 2.00

        The 2.00 OS uses a new mechanism of loading the FPGA. The process will depend on whether you are using Linux or Windows as the ``echo`` command functinality differs bewteen the two.

        Please note that you need to change the forward slashes to backward slashes on Windows.

        1. On Windows, open **Vivado** and use the **TCL console**. Alternatively, use **Vivado HSL Command Prompt** (use Windows search to find it). Navigate to the *.bit* file location.

           On Linux, open the **Terminal** and go to the *.bit* file location.

           .. code-block:: bash

               cd <Path/to/RedPitaya/repository>/prj/Examples/Simple_moving_average/tmp/Simple_moving_average/Simple_moving_average.runs/impl_1

        2. Create *.bif* file and use it to generate a binary bitstream file (*system_wrapper.bit.bin*)

           **Windows (Vivado TCL console or Vivado HSL Command Prompt):**

           .. code-block:: bash

               echo all:{ red_pitaya_top.bit } >  red_pitaya_top.bif
               bootgen -image red_pitaya_top.bif -arch zynq -process_bitstream bin -o red_pitaya_top.bit.bin -w

           **Linux and Windows (WSL + Normal CMD):**

           .. code-block:: bash

               echo -n "all:{ red_pitaya_top.bit }" >  red_pitaya_top.bif
               bootgen -image red_pitaya_top.bif -arch zynq -process_bitstream bin -o red_pitaya_top.bit.bin -w

        3. Using a standard command prompt, send the *.bit.bin* file to the Red Pitaya with the ``scp`` command or use WinSCP or a similar tool to perform the operation.

           .. code-block:: bash
   
               scp red_pitaya_top.bit.bin root@rp-xxxxxx.local:/root/Simple_moving_average.bit.bin

        4. Now establish an SSH communication with your Red Pitaya and check if you have the copy *Simple_moving_average.bit.bin* in the root directory (you can use Putty or WSL).

           .. code-block:: bash

               redpitaya> ls

        5. Finally, we are ready to program the FPGA with our own bitstream file located in the **/root/** folder on Red Pitaya. 
           To program the FPGA simply execute the following line in the Red Pitaya Linux terminal that will load the *Simple_moving_average.bit.bin* image into the FPGA:

           .. code-block:: bash

               redpitaya> fpgautil -b Simple_moving_average.bit.bin

=======
Testing
=======

Connect to the Red Pitaya and start the oscilloscope and connect OUT1 to IN2. Start the generator on the first channel at a frequency of 1 MHz or more. You should see a signal on IN1 even though nothing is connected to it. This is just the filtered moving average data. 

Check whether the FPGA was changed with the following command:

.. code-block:: shell-session
    
    monitor 0x40600050

The Red Pitaya should return FEEDBACC.

In order to setup the filter, we need to connect via SSH and enter the following command:

.. code-block:: shell-session
    
    monitor 0x40600008 3

where 0x40600008 is the address of our register and 3 is the value that should be written in the register.

The result of our filter's work when the register value equals 3:

.. figure:: img/MovingAvg8.png
    :width: 700
    :align: center

The result of our filter's work when the register value equals 2:

.. figure:: img/MovingAvg9.png
    :width: 700
    :align: center

The result of our filter's work when the register value equals 1:

.. figure:: img/MovingAvg10.png
    :width: 700
    :align: center


============================================
Generation the example from the repository
============================================

To create a fully functional project from the example repository, execute the following command:

.. code-block:: shell-session

    cd C:/Users/RedPitaya-FPGA/prj/Examples/Simple_moving_average/
    vivado -source make_project.tcl

Do not forget to change the contents of **make_project.tcl**.

===============
Author & Source
===============

    - Orignal author: Laboratory for Integrated Circuit Design

Original lesson: |lesson link|

Please note that the original site is in Slovene.

.. |lesson link| raw:: html

    <a href="https://lniv.fe.uni-lj.si/xilinx/redpitaya-sito.htm" target="_blank">link</a>
