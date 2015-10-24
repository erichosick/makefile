CC		= g++
CFLAGS	= -Ofast -c -std=c++11 -Wextra -Wall
LDFLAGS	= -std=c++11 -Wextra -Wall

INC_DIR	= inc
SRC_DIR	= src
OBJ_DIR	= obj

OBJS	= multiply.o main.o
OBJSD	= ${OBJ_DIR}/multiply.o ${OBJ_DIR}/main.o
EXE		= multiply

vpath %.h ${INC_DIR}
vpath %.cpp ${SRC_DIR}
vpath %.obj ${OBJ_DIR}

$(EXE): $(OBJS)
	$(CC) $(LDFLAGS) -I${INC_DIR} $(OBJSD) -o $(EXE)

%.o: %.cpp
	$(CC) $(CFLAGS) -I${INC_DIR} $< -o ${OBJ_DIR}/$@

.PHONY : clean

clean :
	-rm -f $(OBJSD) $(EXE)
