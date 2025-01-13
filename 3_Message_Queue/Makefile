# Compiler and flags
CC = gcc
CFLAGS = -Wall -Wextra -g

# Targets
all: sender receiver

sender: sender.c
	$(CC) $(CFLAGS) -o sender sender.c

receiver: receiver.c
	$(CC) $(CFLAGS) -o receiver receiver.c

# Clean up build artifacts
clean:
	rm -f sender receiver