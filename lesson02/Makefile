CC		= g++
CFLAGS	= -Ofast -c -std=c++11 -Wextra -Wall
LDFLAGS	= -std=c++11 -Wextra -Wall

INC_DIR	= inc
SRC_DIR	= src
OBJ_DIR	= obj
BIN_DIR	= bin

OBJ		= subtract.o main2.o
EXE		= ${BIN_DIR}/subtract

vpath %.h ${INC_DIR}
vpath %.cpp ${SRC_DIR}

$(EXE): $(OBJ)
	$(CC) $(LDFLAGS) -I${INC_DIR} $(OBJ) -o $(EXE)

subtract.o: subtract.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/subtract.cpp -o subtract.o

main2.o: main2.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/main2.cpp -o main2.o

.PHONY : clean

clean :
	-rm -f $(OBJ) $(EXE)
