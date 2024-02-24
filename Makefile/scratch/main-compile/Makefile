# rule 1
main: main.o
	echo "RULE 1"
	cc main.o -o main
	echo "SUCCESS"

# rule 2
main.o: main.c
	echo "RULE 2"
	cc -c main.c -o main.o

# rule 3
main.c:
	echo "RULE 3"
	echo "int main() { return 0; }" > main.c
