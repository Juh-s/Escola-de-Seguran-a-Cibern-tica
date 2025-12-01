# Configuração para todas as questões

**Mudar apenas o nome Top entity para o nome da simulação**


<img width="226" height="642" alt="image" src="https://github.com/user-attachments/assets/0f2fd4e1-f3ee-41a2-a251-7d8d5b197e64" />

## Questão 1
**Testbench**
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY tb_ShiftRegister IS
END tb_ShiftRegister;

ARCHITECTURE behavior OF tb_ShiftRegister IS 
    COMPONENT ShiftRegister
    PORT(
         clk : IN  std_logic;
         dir : IN  std_logic;
         data_in : IN  std_logic;
         q_out : OUT  std_logic_vector(3 downto 0)
        );
    END COMPONENT;

    signal clk : std_logic := '0';
    signal dir : std_logic := '0';
    signal data_in : std_logic := '0';
    signal q_out : std_logic_vector(3 downto 0);

BEGIN
    uut: ShiftRegister PORT MAP (
          clk => clk,
          dir => dir,
          data_in => data_in,
          q_out => q_out
        );

    stim_proc: process
    begin		
        clk <= '0';
        wait for 50 ns;

        dir <= '1';
        data_in <= '1';
        wait for 50 ns;
        clk <= '1'; 
        wait for 50 ns;
        clk <= '0';
        wait for 50 ns;

        clk <= '1';
        wait for 50 ns;
        clk <= '0';
        wait for 50 ns;

        dir <= '0';
        data_in <= '0';
        wait for 50 ns;
        clk <= '1';
        wait for 50 ns;
        clk <= '0';
        wait for 50 ns;

        wait;
    end process;
END;
```

**VHDL**

```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY ShiftRegister IS
    PORT ( clk     : in  STD_LOGIC;
           dir     : in  STD_LOGIC;
           data_in : in  STD_LOGIC;
           q_out   : out STD_LOGIC_VECTOR (3 downto 0));
END ShiftRegister;

ARCHITECTURE Behavioral OF ShiftRegister IS
    signal temp_q : STD_LOGIC_VECTOR (3 downto 0) := "0000";
BEGIN
    PROCESS(clk)
    BEGIN
        IF rising_edge(clk) THEN
            IF (dir = '1') THEN
                -- Desloca para Esquerda
                temp_q <= temp_q(2 downto 0) & data_in;
            ELSE
                -- Desloca para Direita
                temp_q <= data_in & temp_q(3 downto 1);
            END IF;
        END IF;
    END PROCESS;
    
    q_out <= temp_q;
END Behavioral;
```

## Questão 2
**Testbench**

```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY tb_CustomCounter IS
END tb_CustomCounter;

ARCHITECTURE behavior OF tb_CustomCounter IS 
    COMPONENT CustomCounter
    PORT(
         clk : IN  std_logic;
         reset : IN  std_logic;
         q_out : OUT  std_logic_vector(3 downto 0)
        );
    END COMPONENT;

    signal clk : std_logic := '0';
    signal reset : std_logic := '0';
    signal q_out : std_logic_vector(3 downto 0); -- 4 bits

BEGIN
    uut: CustomCounter PORT MAP (
          clk => clk,
          reset => reset,
          q_out => q_out
        );

    stim_proc: process
    begin
        reset <= '1';
        wait for 50 ns;
        reset <= '0'; 
        wait for 50 ns;
       
        clk <= '1'; wait for 50 ns; clk <= '0'; wait for 50 ns;

        clk <= '1'; wait for 50 ns; clk <= '0'; wait for 50 ns;
        
        clk <= '1'; wait for 50 ns; clk <= '0'; wait for 50 ns;
        
        clk <= '1'; wait for 50 ns; clk <= '0'; wait for 50 ns;
        
        clk <= '1'; wait for 50 ns; clk <= '0'; wait for 50 ns;
        
        clk <= '1'; wait for 50 ns; clk <= '0'; wait for 50 ns;

        clk <= '1'; wait for 50 ns; clk <= '0'; wait for 50 ns;

        wait;
    end process;
END;
```

**VHDL**


```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY CustomCounter IS
    PORT ( clk   : in  STD_LOGIC;
           reset : in  STD_LOGIC;
           q_out : out STD_LOGIC_VECTOR (3 downto 0));
END CustomCounter;

ARCHITECTURE Behavioral OF CustomCounter IS
    signal current_state : STD_LOGIC_VECTOR (3 downto 0) := "0010"; 
BEGIN
    PROCESS(clk, reset)
    BEGIN
        IF (reset = '1') THEN
            current_state <= "0010";
            
        ELSIF rising_edge(clk) THEN
            CASE current_state IS
                WHEN "0010" => current_state <= "0100";
                WHEN "0100" => current_state <= "0110";
                WHEN "0110" => current_state <= "1000";
                WHEN "1000" => current_state <= "0101";
                WHEN "0101" => current_state <= "0011";
                WHEN "0011" => current_state <= "0010";
                WHEN OTHERS => current_state <= "0010";
            END CASE;
        END IF;
    END PROCESS;
    
    q_out <= current_state;
END Behavioral;
```

## Questão 3
**Testbench FF-D**
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY tb_FF_D IS
END tb_FF_D;

ARCHITECTURE behavior OF tb_FF_D IS 
    COMPONENT FF_D
    PORT( clk : IN std_logic; d : IN std_logic; q : OUT std_logic);
    END COMPONENT;

    signal clk : std_logic := '0';
    signal d : std_logic := '0';
    signal q : std_logic;

BEGIN
    uut: FF_D PORT MAP (clk => clk, d => d, q => q);

    stim_proc: process
    begin
        d <= '1';
        wait for 50 ns;
        clk <= '1'; wait for 50 ns;
        clk <= '0'; wait for 50 ns;

        d <= '0';
        wait for 50 ns;
        clk <= '1'; wait for 50 ns;
        clk <= '0'; wait for 50 ns;

        wait;
    end process;
END;
```
**VHDL**
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY FF_D IS
    PORT ( clk : in STD_LOGIC;
           d   : in STD_LOGIC;
           q   : out STD_LOGIC);
END FF_D;

ARCHITECTURE Behavioral OF FF_D IS
BEGIN
    PROCESS(clk)
    BEGIN
        IF rising_edge(clk) THEN
            q <= d;
        END IF;
    END PROCESS;
END Behavioral;
```

**Testbench FF-T**
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY tb_FF_T IS
END tb_FF_T;

ARCHITECTURE behavior OF tb_FF_T IS 
    COMPONENT FF_T
    PORT( clk : IN std_logic; t : IN std_logic; q : OUT std_logic);
    END COMPONENT;

    signal clk : std_logic := '0';
    signal t : std_logic := '0';
    signal q : std_logic;

BEGIN
    uut: FF_T PORT MAP (clk => clk, t => t, q => q);

    stim_proc: process
    begin
        t <= '0';
        wait for 50 ns;
        clk <= '1'; wait for 50 ns; 
        clk <= '0'; wait for 50 ns;

        t <= '1';
        wait for 50 ns;
        clk <= '1'; wait for 50 ns; 
        clk <= '0'; wait for 50 ns;
        wait for 50 ns;
        clk <= '1'; wait for 50 ns; 
        clk <= '0'; wait for 50 ns;

        wait;
    end process;
END;
```
**VHDL**
```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY FF_T IS
    PORT ( clk : in STD_LOGIC;
           t   : in STD_LOGIC;
           q   : out STD_LOGIC);
END FF_T;

ARCHITECTURE Behavioral OF FF_T IS
    signal temp_q : STD_LOGIC := '0';
BEGIN
    PROCESS(clk)
    BEGIN
        IF rising_edge(clk) THEN
            IF (t = '1') THEN
                temp_q <= not temp_q;
            END IF;
        END IF;
    END PROCESS;
    q <= temp_q;
END Behavioral;
```
