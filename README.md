# **EXP5: Design and Simulation of RAM, ROM, and FIFO Memory with Read and Write Operations Using Verilog HDL**

---

## **Aim**
To design, simulate, and verify the functionality of **RAM**, **ROM**, and **FIFO memory** modules with **read and write operations** using Verilog HDL.

---

## **Apparatus Required**
- System with **Vivado Design Suite**
---

## **Theory**

### **1. Random Access Memory (RAM)**
RAM is a volatile memory used to store data temporarily during program execution. It supports both **read** and **write** operations. Data can be accessed randomly using the address lines.  
In Verilog, RAM can be modeled using a `reg` array with write operation controlled by a **write enable (WE)** signal.

### **2. Read Only Memory (ROM)**
ROM is a non-volatile memory where data is permanently stored and can only be **read**, not written. The contents are initialized during declaration using an `initial` block or `$readmemb`/`$readmemh` commands.

### **3. First In First Out (FIFO) Memory**
FIFO is a sequential buffer that stores data such that the **first data written is the first data read**. It uses two pointers — **write pointer** and **read pointer** — to manage data flow. FIFO finds application in data buffering and inter-module communication.

---

## **Program**

### **1. RAM Module**
```verilog
module ram_4kb (clk,we,addr,din,dout);
input clk;
input we;
input [11:0] addr;
input [7:0] din;
output reg [7:0] dout;

reg [7:0] mem [0:4095];

always @(posedge clk)
begin
    if (we)
        mem[addr] <= din;
    else
        dout <= mem[addr];
end
endmodule
```
### Testbench for RAM
```
`timescale 1ns/1ps
module tb_ram_4kb;

reg clk;
reg we;
reg [11:0] addr;
reg [7:0] din;
wire [7:0] dout;
integer i;

ram_4kb dut (clk,we,addr,din,dout);

initial clk = 0;
always #5 clk = ~clk;

initial begin
    we = 0;
    addr = 0;
    din = 0;
    #10;
    for (i = 0; i < 20; i = i + 1) begin
        @(posedge clk);
        addr = $random % 4096;
        din  = $random % 256;
        we   = 1;
        @(posedge clk);
        we   = 0;
    end
    #20 $finish;
end
endmodule

```
### Simulation Output for RAM

<img width="1044" height="659" alt="image" src="https://github.com/user-attachments/assets/1ced9e9c-9827-415f-9ff2-2d7eb95c052d" />


### 2. ROM Module
```
module rom_4kb (addr,dout);
input [11:0] addr;
output reg [7:0] dout;

reg [7:0] mem [0:4095];
integer i;

initial begin
   for (i = 0; i < 4096; i = i + 1)
       mem[i] = $random % 256;
end

always @(*) begin
   dout = mem[addr];
end
endmodule

```
### Testbench for ROM
```
`timescale 1ns/1ps
module tb_rom_4kb;

reg [11:0] addr;
wire [7:0] dout;
integer i;

rom_4kb dut (addr,dout);

initial begin
    addr = 0;
    #10;
    for (i = 0; i < 20; i = i + 1) begin
        #10 addr = i;
    end
    #20 $finish;
end
endmodule
  
```
### Simulation Output for ROM
*
*
*
*
Paste the output here
*
*


### 3. FIFO Memory Module
```
// 4x8 FIFO Memory with Read and Write Operations
module fifo_4x8 (
    input clk, reset, wr_en, rd_en,
    input [7:0] data_in,
    output reg [7:0] data_out,
    output reg full, empty
);
    reg [7:0] fifo_mem [3:0];
    reg [1:0] wr_ptr, rd_ptr;
    reg [2:0] count;

    always @(posedge clk or posedge reset) begin
        if (reset) begin
            wr_ptr <= 0;
            rd_ptr <= 0;
            count <= 0;
            full <= 0;
            empty <= 1;
        end
        else begin
            // Write operation
            if (wr_en && !full) begin
                fifo_mem[wr_ptr] <= data_in;
                wr_ptr <= wr_ptr + 1;
                count <= count + 1;
         
    end
endmodule
```
### Testbench for FIFO
```
module tb_fifo_4x8;
    reg clk, reset, wr_en, rd_en;
    reg [7:0] data_in;
    wire [7:0] data_out;
    wire full, empty;

    fifo_4x8 uut(clk, reset, wr_en, rd_en, data_in, data_out, full, empty);

    always #5 clk = ~clk;

    initial begin
        clk = 0; reset = 1; wr_en = 0; rd_en = 0; data_in = 8'h00;
        #10 reset = 0;

        // Write data
        wr_en = 1; data_in = 8'h11; #10;
        data_in = 8'h22; #10;
        
endmodule
```
### Simulation Output for FIFO
*
*
*
*
Paste the output here
*
*
### Result

The RAM, ROM, and FIFO memory modules were successfully designed, simulated, and verified using Verilog HDL in Vivado Design Suite.
All read and write operations performed as expected during simulation.
