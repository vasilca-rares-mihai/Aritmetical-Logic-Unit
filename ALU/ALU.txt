--------------------------------------------
-- Module Name: tutorial
--------------------------------------------

library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use IEEE.STD_LOGIC_ARITH.ALL;
use IEEE.STD_LOGIC_UNSIGNED.ALL;


library UNISIM;
use UNISIM.VComponents.all;

Entity ALU Is
port (
		A, B : IN STD_LOGIC_VECTOR(1 DOWNTO 0);
		OPCODE : IN STD_LOGIC_VECTOR(1 DOWNTO 0);
		led : out STD_LOGIC_VECTOR(5 downto 0) := "000000";
		seg : out std_logic_vector(6 downto 0);
		dp: out std_logic;
		an : out std_logic_vector(3 downto 0)
	);
end ALU;

Architecture behavior of ALU Is
signal C : std_logic_vector(3 downto 0);

begin
process(OPCODE, A, B)
    begin

an(0)<='0';
an(1)<='1';
an(2)<='1';
an(3)<='1';
dp<='1';

led(0) <= A(0);
led(1) <= A(1);

led(2) <= B(0);
led(3) <= B(1);

led(4) <= OPCODE(0);
led(5) <= OPCODE(1);

case OPCODE is
when "00" => -- adunare
 C(0) <= A(0) xor B(0);
 C(1) <= (A(1) xor B(1)) xor (A(0) and B(0));
 C(2) <= (A(1) and B(1)) or ((A(1) xor B(1)) and (A(0) and B(0)));
  
when "01" => -- scadere
if A(1)>B(1) or (A(1)=B(1) and A(0)>B(0)) then
C(0) <= (not (A(0)) and B(0)) or (A(0) and not (B(0)));
C(1) <= (A(1) and not (B(1)) and not (B(0))) or (A(1) and A(0) and not(B(1)));
 else 
C(0) <= (not (B(0)) and A(0)) or (B(0) and not (A(0)));
 C(1) <= (B(1) and not (A(1)) and not (A(0))) or (B(1) and B(0) and not(A(1)));
-- an(0)<='1';
 --an(1)<='0';
 --seg(6)<='0';

end if;

when "10" => -- inmultire
 C(0) <= A(0) and B(0);
 C(1) <= (A(1) and B(0)) xor (A(0) and B(1));
 C(2) <= (A(1) and B(1)) xor ((A(0) and B(1)) and (B(0) and A(1)));
 C(3) <= ( A(1) and B(1) ) and ( (A(1) and B(0) ) and (A(0) and B(1)) );
 
when "11" => -- impartire

if (A="10" and B="11") then
dp<='0';

elsif (A="11" and B="10") then 
dp<='0';

elsif (A="01" and B="10") then 
dp<='0';

elsif (A="01" and B="11") then 
dp<='0';

elsif (B="00") then
an(0)<='0';
an(1)<='0';
an(2)<='0';
an(3)<='0';
seg<="0000100";

else
 C(1) <= A(1) and not(B(1));
 C(0) <= (A(0) and not(B(1))) or (A(1) and not(B(0))) or (A(1) and A(0));
 

 
end if;
 when others=>null;
 
 
 
end case;


seg(0) <= (C(2) and not(C(1)) and not(C(0))) or (not(C(3)) and not(C(2)) and not(C(1)) and C(0)); 

seg(1) <= (C(2) and not(C(1)) and C(0)) or (C(2) and C(1) and not(C(0))); 

seg(2) <= (not(C(2)) and C(1) and not(C(0))); 

seg(3) <= (C(2) and not(C(1)) and not(C(0))) or (C(2) and C(1) and C(0)) or (not(C(3)) and not(C(2)) and not(C(1)) and C(0)); 

seg(4) <= C(0) or (C(2) and not(C(1))); 

seg(5) <= (not(C(3)) and not(C(2)) and C(0)) or (C(1) and C(0)) or (not(C(3)) and not(C(2)) and C(1)); 

seg(6) <= (not(C(3)) and not(C(2)) and not(C(1))) or (C(2) and C(1) and C(0)); 

 

end process;


end behavior;