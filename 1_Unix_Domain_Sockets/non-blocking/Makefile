# Compiler and flags
CC = gcc
CFLAGS = -Wall -Wextra -g

# Targets
all: client server

client: client.c
	$(CC) $(CFLAGS) -o client client.c

server: server.c
	$(CC) $(CFLAGS) -o server server.c

# Clean up build artifacts
clean:
	rm -f client server