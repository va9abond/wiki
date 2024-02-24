thing_wrong := *.o             # Bad, '*' will not get expanded
thing_right := $(wildcard *.o) # Nice!

all: one two three four

# Fails, because $(thing_wrong) is the string "*.o"
one: $(thing_wrong)

$ Stays as *.o if there are no files that match this pattern
two: *.o

# Works as you would expect: in this case, it does nothing
three: $(thing_right)

# Same as rule 'three'
four: $(wildcard *.o)
