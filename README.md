# EXPERIMENT 3: Simulation of All Flip-Flops using Blocking Statement

## AIM
To design and simulate basic flip-flops (SR, D, JK, and T) using **blocking statements** in Verilog HDL, and verify their functionality through simulation in Vivado 2023.1.

## APPARATUS REQUIRED
- Vivado 2023.1
- Computer with HDL Simulator

## DESCRIPTION
Flip-flops are the basic memory elements in sequential circuits.  
In this experiment, different types of flip-flops (SR, D, JK, T) are modeled using **behavioral modeling** with **blocking assignment (`=`)** inside the `always` block.  
Blocking assignments execute sequentially in the given order, which makes it easier to describe simple synchronous circuits.

## PROCEDURE
1. Open **Vivado 2023.1**.  
2. Create a **New RTL Project** (e.g., `FlipFlop_Simulation`).  
3. Add Verilog source files for each flip-flop (SR, D, JK, T).  
4. Add a testbench file to verify all flip-flops.  
5. Run **Behavioral Simulation**.  
6. Observe waveforms of inputs and outputs for each flip-flop.  
7. Verify that outputs match the truth table.  
8. Save results and capture simulation screenshots.

---

## VERILOG CODE

### SR Flip-Flop (Blocking)
```verilog
`timescale 1ns / 1ps
module sr_flipflop(
    input clk,
    input s,
    input r,
    input reset,
    output reg q
);

    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 0;
        else begin
            case ({s, r})
                2'b00: q <= q;     // No change
                2'b01: q <= 0;     // Reset
                2'b10: q <= 1;     // Set
                2'b11: q <= 1'bx;  // Invalid state
            endcase
        end
    end

endmodule




```
### SR Flip-Flop Test bench 
```verilog
`timescale 1ns / 1ps
module sr_flipflop_tb;
    reg clk, s, r, reset;
    wire q;

    sr_flipflop uut (
        .clk(clk),
        .s(s),
        .r(r),
        .reset(reset),
        .q(q)
    );

    initial clk = 0;
    always #5 clk = ~clk; // 10ns clock period

    initial begin
        $monitor("Time=%0t | clk=%b | S=%b R=%b reset=%b | Q=%b", $time, clk, s, r, reset, q);

        reset = 1; s = 0; r = 0; #10;
        reset = 0; s = 0; r = 0; #10; // No change
        s = 1; r = 0; #10;             // Set
        s = 0; r = 0; #10;             // Hold
        s = 0; r = 1; #10;             // Reset
        s = 0; r = 0; #10;             // Hold
        s = 1; r = 1; #10;             // Invalid
        s = 0; r = 0; #10;             // Hold

        $finish;
    end
endmodule




```
#### SIMULATION OUTPUT
![WhatsApp Image 2025-09-18 at 01 24 24_a033619a](https://github.com/user-attachments/assets/5615ea50-bce1-4a32-b980-834937f5be44)


### JK Flip-Flop (Blocking)
```verilog
`timescale 1ns / 1ps
module jk_flipflop (
    input clk,
    input j,
    input k,
    output reg q,
    output reg qbar
);

    always @(posedge clk) begin
        
        if (j == 0 && k == 0) begin
            q = q;       
            qbar = ~q;
        end else if (j == 0 && k == 1) begin
            q = 0;      
            qbar = 1;
        end else if (j == 1 && k == 0) begin
            q = 1;       
            qbar = 0;
        end else if (j == 1 && k == 1) begin
            q = ~q;     
            qbar = ~qbar;
        end
    end
endmodule


```
### JK Flip-Flop Test bench 
```verilog
`timescale 1ns / 1ps
module jk_flipflop_tb;
    reg clk, j, k;
    wire q, qbar;

    jk_flipflop uut (
        .clk(clk),
        .j(j),
        .k(k),
        .q(q),
        .qbar(qbar)
    );

    initial begin
        clk = 0;
        forever #5 clk = ~clk;  // Clock period 10ns
    end

    initial begin
        $monitor("Time=%0t | clk=%b | J=%b K=%b | Q=%b Qbar=%b", $time, clk, j, k, q, qbar);

        // Initialize inputs
        j = 0; k = 0; #10;

        // No change
        j = 0; k = 0; #10;

        // Reset
        j = 0; k = 1; #10;

        // Set
        j = 1; k = 0; #10;

        // Toggle
        j = 1; k = 1; #20;

        // Back to reset
        j = 0; k = 1; #10;

        // Toggle
        j = 1; k = 1; #20;

        // No change
        j = 0; k = 0; #10;

        $finish;
    end
endmodule


```
#### SIMULATION OUTPUT

![WhatsApp Image 2025-09-18 at 01 32 40_3e606102](https://github.com/user-attachments/assets/9a8bf04c-da4e-4ffa-8802-97d1fe3fc6e0)

### D Flip-Flop (Blocking)
```verilog
`timescale 1ns / 1ps
module d_flipflop(
    input clk,
    input d,
    input reset,
    output reg q
);

    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 0;
        else
            q <= d;
    end

endmodule

```
### D Flip-Flop Test bench 
```verilog
`timescale 1ns / 1ps
module d_flipflop_tb;
    reg clk, d, reset;
    wire q;

    d_flipflop uut (
        .clk(clk),
        .d(d),
        .reset(reset),
        .q(q)
    );

    initial clk = 0;
    always #5 clk = ~clk;  // 10ns clock period

    initial begin
        $monitor("Time=%0t | clk=%b | d=%b reset=%b | q=%b", $time, clk, d, reset, q);

        reset = 1; d = 0; #10;
        reset = 0; d = 0; #10;
        d = 1; #10;
        d = 0; #10;
        d = 1; #10;
        d = 1; #10;
        d = 0; #10;

        $finish;
    end
endmodule



```

#### SIMULATION OUTPUT

![WhatsApp Image 2025-09-18 at 01 14 17_69d27688](https://github.com/user-attachments/assets/19a564fa-c889-4206-bf87-beebf592111d)

### T Flip-Flop (Blocking)
```verilog
`timescale 1ns / 1ps
module t_flipflop(
    input clk,
    input t,
    input reset,
    output reg q
);

    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 1'b0;
        else if (t)
            q <= ~q;      // Toggle when T is 1
        else
            q <= q;       // Hold state when T is 0
    end

endmodule



endmodule
```
### T Flip-Flop Test bench 
```verilog
`timescale 1ns / 1ps
module t_flipflop_tb;
    reg clk;
    reg t;
    reg reset;
    wire q;

    t_flipflop uut (
        .clk(clk),
        .t(t),
        .reset(reset),
        .q(q)
    );

    initial begin
        clk = 0;
        forever #5 clk = ~clk;  // Clock period 10 units
    end

    initial begin
        reset = 1; t = 0;
        #10 reset = 0;

        #10 t = 0;          // Hold
        #10 t = 1;          // Toggle
        #20 t = 1;          // Toggle again
        #10 t = 0;          // Hold
        #10 t = 1;          // Toggle
        #10 $finish;
    end

    initial begin
        $monitor("Time=%0t | clk=%b | reset=%b | t=%b | q=%b", $time, clk, reset, t, q);
    end

endmodule



```

#### SIMULATION OUTPUT

![WhatsApp Image 2025-09-18 at 01 39 55_6dc56c0c](https://github.com/user-attachments/assets/d0f50668-8733-427b-9e96-0daf10ff492f)


### RESULT

All flip-flops (SR, D, JK, T) were successfully simulated using blocking statements in Verilog HDL.
The outputs matched the expected truth table values, demonstrating correct sequential behavior.
