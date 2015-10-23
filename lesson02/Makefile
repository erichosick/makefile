CC		= g++
CFLAGS	= -Ofast -c -std=c++11 -Wextra -Wall
LDFLAGS	= -std=c++11 -Wextra -Wall

INC_DIR	= inc
SRC_DIR	= src
OBJ_DIR	= obj

OBJ		= ${OBJ_DIR}/subtract.o ${OBJ_DIR}/main.o
EXE		= subtract

vpath %.h ${INC_DIR}
vpath %.cpp ${SRC_DIR}
vpath %.obj ${OBJ_DIR}

$(EXE): $(OBJ)
	$(CC) $(LDFLAGS) -I${INC_DIR} $(OBJ) -o $(EXE)

${OBJ_DIR}/subtract.o: subtract.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/subtract.cpp -o ${OBJ_DIR}/subtract.o

${OBJ_DIR}/main.o: main.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/main.cpp -o ${OBJ_DIR}/main.o

.PHONY : clean

clean :
	-rm -f $(OBJ) $(EXE)
