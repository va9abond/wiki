objs := foo.o bar.o all.o

all: $(objs)

# These files compile via implicit rules
foo.o: foo.c
bar.o: bar.c
all.o: all.c

all.c:
	echo "int main() { return 0; }" > all.c

(wildcard %.c):
	touch $@

clean:
	rm -f *.c *.o all

