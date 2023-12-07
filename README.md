# a simple Frequency counter with an equivalent precision measurement

## Principle

Neither the frequency measurement nor the period measurement are exact multiples of the clock cycle within the measurement time t. It will create an error of ±1 clock cycle. Consequently, neither of them perform well at both high and low frequencies. 

We set a standard clock(clk_stand), with a known frequency(f~s~) as a reference to determine the frequency(f~x~) of the measured clock(clk_test). 

![](E:\IC_design\Verilog\FPGA_S6\freq_meter\doc\equivalent precision measurement.bmp)

I plan a complete cycle for gate_a with a duration of 1.5 seconds. The first 0.25 seconds is low for resetting various counters, the middle 1 second is high for calculating the number of clk cycles for clk_test and clk_stand. At last the final 0.25 seconds is low again. After total 1.5 seconds, calculate the frequency f~x~ of clk_test. 

The measurement time T~X~ is an integer multiple of the clk_test period, eliminating the error of ±1 clock cycle generated by clk_test. However, it will also introduce an error of ±1 clock cycle for clk_stand, as T~X~ is not necessarily an integer multiple of the clk_stand period. This error can be reduced and measurement accuracy improved by increasing T~X~ or raising the frequency f~s~ of clk_stand. We set T~X~ = 1s and f~s~ = 100Mhz. 

## calculation

1. Calculate the number X of clock cycles for clk_test in period of T~X~ . Set f~X~ ist the frequency of clk_test. T~x~ = X/f~X~
2. Calculate the number Y of clock cycles for clk_stand in period of T~X~ . Set f~Y~ ist the frequency of clk_stand. T~x~ = Y/f~Y~
3. X/f~X~ = Y/f~Y~ , f~X~ = X*f~s~/Y

## implementation

In this architecture, I need 3 clock inputs. They are system clock cycle sys_clk(50Mhz), measured clock clk_test, standard clock clk_stand(100Mhz). I use PLL to generate clk_stand. 

**NOTICE: In ISE, if a built-in IP core is introduced, for example, a PLL generates 100Mhz (clk_stand) from a 50Mhz (sys_clk). This PLL reads the sys_clk on the FPGA into CLK_IN1, generating a clock output CLK_OUT1 with a frequency of 100Mhz (clk_stand). This input is buffered, so after passing through this PLL module, the sys_clk on the FPGA cannot be directly read again, otherwise it will cause an error: Xst: 2035. **

**SOLUTION: This PLL generates another 50Mhz clock as sys_clk for the subsequent modules.**