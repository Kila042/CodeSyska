FUNCTION_BLOCK POU_1
VAR_INPUT
END_VAR
VAR_OUTPUT
END_VAR
VAR
start_trig : r_trig;
stop_trig : r_trig;
motor : BOOL;
stop : BOOL;
state : INT := 10;
call_POU_2 : POU_2;
bip_count : INT:=5;
END_VAR

CASE state OF
	10:
		 start_trig(CLK:=gvl.start);
		 IF start_trig.Q = TRUE THEN
			 gvl.start := TRUE;
			 state := 11;
		 END_IF
		 
	11:
		 IF call_POU_2.counter < bip_count THEN
			 call_POU_2(time_1:=T#800MS);
		 ELSE
			 state := 20;
		 END_IF
		 
	20: 
		 motor := TRUE;
		 state := 30;
		 
	30:
		 stop_trig(clk:=gvl.stop);
		 IF stop_trig.Q = TRUE THEN
			 gvl.start := FALSE;
			 motor := FALSE;
			 call_POU_2(reset:=TRUE);
			 call_POU_2(reset:=FALSE);
			 state := 10;
		 END_IF
END_CASE
