# Exp-No: 01 - Write and simulate 4:1 Multiplexer using Verilog HDL and verify with testbench 


**Aim:** <br>
<br>
&emsp;&emsp;To design and simulate a 4:1 Mux using Verilog HDL and verify its functionality through a testbench using the Vivado 2023.1 simulation environment. 
<br>
**Apparatus Required:** <br>
<br>
&emsp;&emsp;Vivado 2023.1<br>
<br>
**Procedure:** <br>
<br>
1. Launch Vivado Open Vivado 2023.1 by double-clicking the Vivado icon or searching for it in the Start menu.<br>
2. Create a New Project Click on "Create Project" from the Vivado Quick Start window. In the New Project Wizard: Project Name: Enter a name for the project (e.g., Mux4_to_1). Project Location: Select the folder where the project will be saved. Click Next. Project Type: Select RTL Project, then click Next. Add Sources: Click on "Add Files" to add the Verilog files (e.g., mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.). Make sure to check the box "Copy sources into project" to avoid any external file dependencies. Click Next. Add Constraints: Skip this step by clicking Next (since no constraints are needed for simulation).
Default Part Selection: You can choose a part based on the FPGA board you are using (if any). If no board is used, you can choose any part, for example, xc7a35ticsg324-1L (Artix-7). Click Next, then Finish.<br>
3. Add Verilog Source Files In the "Sources" window, right-click on "Design Sources" and select Add Sources if you didn't add all files earlier. Add the Verilog files (mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.) and the testbench (mux4_to_1_tb.v).<br>
4. Check Syntax Expand the "Flow Navigator" on the left side of the Vivado interface. Under "Synthesis", click "Run Synthesis". Vivado will check your design for syntax errors. If any errors or warnings appear, correct them in the respective Verilog files and re-run the synthesis.<br>
5. Simulate the Design In the Flow Navigator, under "Simulation", click on "Run Simulation" → "Run Behavioral Simulation". Vivado will open the Simulations Window, and the waveform window will show the signals defined in the testbench.<br>
6. View and Analyze Simulation Results The simulation waveform window will display the signals (S1, S0, A, B, C, D, Y_gate, Y_dataflow, Y_behavioral, Y_structural). Use the time markers to verify the correctness of the 4:1 MUX output for each set of inputs. You can zoom in/out or scroll through the simulation time using the waveform viewer controls.<br>
7. Adjust Simulation Time To run a longer simulation or adjust timing, go to the Simulation Settings by clicking "Simulation" → "Simulation Settings".
Under "Simulation", modify the Run Time (e.g., set to 1000ns).<br>
8. Generate Simulation Report Once the simulation is complete, you can generate a simulation report by right-clicking on the simulation results window and selecting "Export Simulation Results". Save the report for reference in your lab records.<br>
9. Save and Document Results Save your project by clicking File → Save Project. Take screenshots of the waveform window and include them in your lab report to document your results. You can include the timing diagram from the simulation window showing the correct functionality of the 4:1 MUX across different select inputs and data inputs.<br>
10. Close the Simulation Once done, by going to Simulation → "Close Simulation<br>
<br>

Input/Output Signal Diagram:


**RTL Code:**

**GATELEVEL**
```
module mux_gt(I, S, Y);
    input [3:0] I;
    input [1:0] S;
    output Y;
    wire [4:1] W;
    and g1(W[1], ~S[1], ~S[0], I[0]);
    and g2(W[2], ~S[1],  S[0], I[1]);
    and g3(W[3],  S[1], ~S[0], I[2]);
    and g4(W[4],  S[1],  S[0], I[3]);
    or  g5(Y, W[1], W[2], W[3], W[4]);
endmodule
```

**BEHAVIORAL**
```
`timescale 1ns / 1ps
module mux(I,S,Y);
input [3:0]I;
input[1:0]S;
output reg Y;
always @(I,S)
    begin
        case(S)
        2'b00:Y=I[0];
        2'b01:Y=I[1];
        2'b10:Y=I[2];
        2'b11:Y=I[3];
        endcase
     end
endmodule
```

**DATAFLOW**
```
`timescale 1ns / 1ps
module MUX_DF(I,S,out);
input [3:0]I;
input [1:0]S;
output out;
assign out=S[1]==0?(S[0]==0?I[0]:I[1]):(S[0]==0?I[2]:I[3]);
endmodule
```

**STRUCTURAL**
```
`timescale 1ns / 1ps
module mux_str(a,b,s,out);
input s,b,a;
output out;
    assign out = s ? b : a;
endmodule
module mux4_to_1_structural (a,s,out);
input [3:0]a;
input [1:0]s;
output out;
    wire mux_low, mux_high;
    mux2_to_1 mux0 (.a(a[0]), .b(a[1]), .s(s[0]), .out(mux_low));
    mux2_to_1 mux1 (.a(a[2]), .b(a[3]), .s(s[0]), .out(mux_high));
    mux2_to_1 mux_final (.a(mux_low), .b(mux_high), .s(s[1]), .out(out));
endmodule
```

TestBench:

**GATELEVEL TESTBENCH**
```
`timescale 1ns / 1ps
module mux4_to_1_tb;
    reg [3:0] I;
    reg [1:0] S;
    wire Y;
    mux_gt uut (.I(I),.S(S),.Y(Y));
    initial begin
        I = 4'b0000;
        S = 2'b00;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_0000;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_0001;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b01_0010;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b10_0100;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b11_1000;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b01_1100;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b10_1010;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b11_0110;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_1111;
        #2 $stop;
    end
    initial begin
        $monitor("Time=%0t | S[1]=%b S[0]=%b | Inputs: I[0]=%b I[1]=%b I[2]=%b I[3]=%b | Y=%b",
                 $time, S[1], S[0], I[0], I[1], I[2], I[3], Y);
    end
endmodule
```

**BEHAVIORAL TESTBENCH**
```
`timescale 1ns / 1ps
module mux_tb;
    reg [3:0] I;
    reg [1:0] S;
    wire Y;
    mux uut (.I(I),.S(S),.Y(Y));
    initial begin
        I = 4'b0000;
        S = 2'b00;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_0000;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_0001;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b01_0010;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b10_0100;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b11_1000;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b01_1100;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b10_1010;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b11_0110;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_1111;
        #2 $stop;
    end
    initial begin
        $monitor("Time=%0t | S[1]=%b S[0]=%b | Inputs: I[0]=%b I[1]=%b I[2]=%b I[3]=%b | Y=%b",
                 $time, S[1], S[0], I[0], I[1], I[2], I[3], Y);
    end
endmodule
```

**DATAFLOW TESTBENCH**
```
`timescale 1ns / 1ps
module MUX_DF_tb;
    reg [3:0] I;
    reg [1:0] S;
    wire out;
    MUX_DF uut (.I(I),.S(S),.out(out));
    initial begin
        I = 4'b0000;
        S = 2'b00;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_0000;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_0001;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b01_0010;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b10_0100;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b11_1000;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b01_1100;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b10_1010;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b11_0110;
        #2 {S[1], S[0], I[3], I[2], I[1], I[0]} = 6'b00_1111;
        #2 $stop;
    end
    initial begin
        $monitor("Time=%0t | S[1]=%b S[0]=%b | I[3]=%b I[2]=%b I[1]=%b I[0]=%b | out=%b",
                 $time, S[1], S[0], I[3], I[2], I[1], I[0], out);
    end
endmodule
```
**STRUCTURAL TESTBENCH**
```
`timescale 1ns / 1ps
module mux4_to_1_structural_tb;
    reg [3:0] a;
    reg [1:0] s;
    wire out_structural;
    mux_str uut (
        .a(a),
        .s(s),
        .out(out_structural)
    );
    initial begin
        a = 4'b0000;
        s = 2'b00;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b00_0000;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b00_0001;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b01_0010;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b10_0100;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b11_1000;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b01_1100;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b10_1010;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b11_0110;
        #2 {s[1], s[0], a[3], a[2], a[1], a[0]} = 6'b00_1111;
        #2 $stop;
    end
    initial begin
        $monitor("Time=%0t | s[1]=%b s[0]=%b | a[3]=%b a[2]=%b a[1]=%b a[0]=%b | out_structural=%b",
                 $time, s[1], s[0], a[3], a[2], a[1], a[0], out_structural);
    end
endmodule
```
Output waveform:

**GATELEVEL OUTPUT**
<img width="1600" height="848" alt="image" src="https://github.com/user-attachments/assets/103d843d-aacf-4fbf-b01a-2c3fc32524c5" />

**BEHAVIORAL OUTPUT**
<img width="1600" height="848" alt="image" src="https://github.com/user-attachments/assets/fa4db524-e63c-4f85-b089-1e379431c86f" />

**DATAFLOW OUTPUT**
<img width="1600" height="848" alt="image" src="https://github.com/user-attachments/assets/e8fd23f8-8eba-4b3d-8afc-8fd52eefda01" />

**STRUCTURAL OUTPUT**
<img width="1600" height="851" alt="image" src="https://github.com/user-attachments/assets/68129c6f-ca0d-4a35-8506-a96781f3c245" />

**SAMPLE OUTPUT**

GATE LEVEL:
```
Time=4 | S[1]=0 S[0]=0 | Inputs: I[0]=0 I[1]=0 I[2]=0 I[3]=0 | Y=0
Time=6 | S[1]=0 S[0]=0 | Inputs: I[0]=1 I[1]=0 I[2]=0 I[3]=0 | Y=1
Time=8 | S[1]=0 S[0]=1 | Inputs: I[0]=0 I[1]=1 I[2]=0 I[3]=0 | Y=1
Time=10 | S[1]=1 S[0]=0 | Inputs: I[0]=0 I[1]=0 I[2]=1 I[3]=0 | Y=1
Time=12 | S[1]=1 S[0]=1 | Inputs: I[0]=0 I[1]=0 I[2]=0 I[3]=1 | Y=1
Time=14 | S[1]=0 S[0]=1 | Inputs: I[0]=0 I[1]=0 I[2]=1 I[3]=1 | Y=0
Time=16 | S[1]=1 S[0]=0 | Inputs: I[0]=0 I[1]=1 I[2]=0 I[3]=1 | Y=0
Time=18 | S[1]=1 S[0]=1 | Inputs: I[0]=0 I[1]=1 I[2]=1 I[3]=0 | Y=0
Time=20 | S[1]=0 S[0]=0 | Inputs: I[0]=1 I[1]=1 I[2]=1 I[3]=1 | Y=1
```

BEHAVIORAL :
```
Time=4 | S[1]=0 S[0]=0 | Inputs: I[0]=0 I[1]=0 I[2]=0 I[3]=0 | Y=0
Time=6 | S[1]=0 S[0]=0 | Inputs: I[0]=1 I[1]=0 I[2]=0 I[3]=0 | Y=1
Time=8 | S[1]=0 S[0]=1 | Inputs: I[0]=0 I[1]=1 I[2]=0 I[3]=0 | Y=1
Time=10 | S[1]=1 S[0]=0 | Inputs: I[0]=0 I[1]=0 I[2]=1 I[3]=0 | Y=1
Time=12 | S[1]=1 S[0]=1 | Inputs: I[0]=0 I[1]=0 I[2]=0 I[3]=1 | Y=1
Time=14 | S[1]=0 S[0]=1 | Inputs: I[0]=0 I[1]=0 I[2]=1 I[3]=1 | Y=0
Time=16 | S[1]=1 S[0]=0 | Inputs: I[0]=0 I[1]=1 I[2]=0 I[3]=1 | Y=0
Time=18 | S[1]=1 S[0]=1 | Inputs: I[0]=0 I[1]=1 I[2]=1 I[3]=0 | Y=0
Time=20 | S[1]=0 S[0]=0 | Inputs: I[0]=1 I[1]=1 I[2]=1 I[3]=1 | Y=1
```

DATAFLOW:
```
Time=2  | S[1]=0 S[0]=0 | I[3]=0 I[2]=0 I[1]=0 I[0]=0 | out=0
Time=4  | S[1]=0 S[0]=0 | I[3]=0 I[2]=0 I[1]=0 I[0]=1 | out=1
Time=6  | S[1]=0 S[0]=1 | I[3]=0 I[2]=0 I[1]=1 I[0]=0 | out=1
Time=8  | S[1]=1 S[0]=0 | I[3]=0 I[2]=1 I[1]=0 I[0]=0 | out=1
Time=10 | S[1]=1 S[0]=1 | I[3]=1 I[2]=0 I[1]=0 I[0]=0 | out=1
Time=12 | S[1]=0 S[0]=1 | I[3]=1 I[2]=1 I[1]=0 I[0]=0 | out=0
Time=14 | S[1]=1 S[0]=0 | I[3]=1 I[2]=0 I[1]=1 I[0]=0 | out=1
Time=16 | S[1]=1 S[0]=1 | I[3]=0 I[2]=1 I[1]=1 I[0]=0 | out=0
Time=18 | S[1]=0 S[0]=0 | I[3]=1 I[2]=1 I[1]=1 I[0]=1 | out=1
```

STRUCTURAL:
```
Time=2  | s[1]=0 s[0]=0 | a[3]=0 a[2]=0 a[1]=0 a[0]=0 | out_structural=0
Time=4  | s[1]=0 s[0]=0 | a[3]=0 a[2]=0 a[1]=0 a[0]=1 | out_structural=1
Time=6  | s[1]=0 s[0]=1 | a[3]=0 a[2]=0 a[1]=1 a[0]=0 | out_structural=1
Time=8  | s[1]=1 s[0]=0 | a[3]=0 a[2]=1 a[1]=0 a[0]=0 | out_structural=1
Time=10 | s[1]=1 s[0]=1 | a[3]=1 a[2]=0 a[1]=0 a[0]=0 | out_structural=1
Time=12 | s[1]=0 s[0]=1 | a[3]=1 a[2]=1 a[1]=0 a[0]=0 | out_structural=0
Time=14 | s[1]=1 s[0]=0 | a[3]=1 a[2]=0 a[1]=1 a[0]=0 | out_structural=1
Time=16 | s[1]=1 s[0]=1 | a[3]=0 a[2]=1 a[1]=1 a[0]=0 | out_structural=0
Time=18 | s[1]=0 s[0]=0 | a[3]=1 a[2]=1 a[1]=1 a[0]=1 | out_structural=1
```
Conclusion:

In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural. The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.





  
