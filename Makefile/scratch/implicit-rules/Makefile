CC = gcc     # Flag for implicit rules
CFLAGS = -g  # Flag for implicit rules. Turn on debug info

# Implicit rule #1: main is built via the C linker implicit rule
# Implicit rule #2: main.o is built via the C compilation implicit rule,
	# because main.c exists
main: main.o

clean:
	rm -f main*

