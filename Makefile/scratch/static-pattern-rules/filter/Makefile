obj_files = foo.result bar.o lose.o
src_files = foo.raw bar.c lose.c

all: $(obj_files)
# Note: PHONY is important here. Without it, implicit rules will try to build
# the executable "all", since the prereqs are ".o" files.
.PHONY: all 

# Ex 1: .o files depend on .c files.
$(filter %.o,$(obj_files)): %.o: %.c
	@echo "target: $@ prereq: $<"

# Ex 2: .result files depend on .raw files.
$(filter %.result,$(obj_files)): %.result: %.raw
	@echo "target: $@ prereq: $<" 

%.raw:
	@echo "raw file: " $@
	@touch $@

%.c:
	@echo "c file: " $@
	@touch $@


clean:
	rm -f $(src_files)
