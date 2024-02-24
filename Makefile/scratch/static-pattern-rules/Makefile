objects := foo.o bar.o all.o
all: $(objects)

# These files compile via implicit rules
# Syntax - targets...: target-patter: prereq-patterns...

# In the case of the first target, foo.o, the target-pattern
# matches foo.o and sets "stem" to be "foo". It then replaces the
# '%' in prereq-patterns with that stem
$(objects): %.o: %.c

all.c:
	echo "int main() { return 0; }" > main.c

%.c
	touch $@

clean:
	rm -f *.o *.c all
