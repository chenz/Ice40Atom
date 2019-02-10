
TOP = atom
NAME = atom
PACKAGE = tq144:4k

YOSYS = yosys
NEXTPNR = nextpnr-ice40
ICEPACK = icepack
ICETIME = icetime

SRCS = ../src/atom.v ../src/cpu.v ../src/ALU.v ../src/rom_c000_f000.v ../src/mc6847.v ../src/charrom.v ../src/vid_ram.v ../src/keyboard.v ../src/ps2_intf.v ../src/bootstrap.v ../src/spi.v ../src/m6522.v ../src/sid/sid_6581.v ../src/sid/sid_coeffs.v ../src/sid/sid_components.v ../src/sid/sid_filters.v ../src/sid/sid_voice.v

SYNDEFINES = -Dblackice2 -Duse_sb_io

-include config.mk

ifeq ($(USE_SID), 1)
SYNDEFINES += -Duse_sid
endif

MKFILES = Makefile $(wildcard config.mk)

all: $(NAME).bin

timing: $(NAME)-timing.txt

clean:
	$(RM) $(NAME).json $(NAME).txt $(NAME).bin $(NAME)-timing.txt

.PHONY: all timing clean

$(NAME).json: $(SRCS) $(MKFILES)
	$(YOSYS) -q -f "verilog $(SYNDEFINES)" -l $(NAME).log -p "synth_ice40 -top $(TOP) -abc2 -json $@" $(SRCS)

$(NAME).txt: $(NAME).json blackice.pcf pre_pack.py $(MKFILES)
	$(NEXTPNR) --hx8k --package $(PACKAGE) --json $(NAME).json --pcf blackice.pcf --pre-pack pre_pack.py --asc $(NAME).txt

$(NAME).bin: $(NAME).txt
	$(ICEPACK) $(NAME).txt $@
	truncate -s 135104 $@

$(NAME)-timing.txt: $(NAME).txt $(MKFILES)
	$(ICETIME) -d hx8k -P $(PACKAGE) -r $@ $(NAME).txt
