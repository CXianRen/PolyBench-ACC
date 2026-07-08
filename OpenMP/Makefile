# Compiler Options
CC = clang

CFLAGS = -O3 -g -fopenmp -DPOLYBENCH_TIME ${CFLAGS_EXTRA}
LDLIBS = -lm

BUILD_DIR = build
BENCHMARK_LIST = utilities/benchmark_list

SOURCES := $(shell grep -v "^#" $(BENCHMARK_LIST))
SOURCES := $(patsubst ./%,%,$(SOURCES))

# Fail list to exclude
FAILING_KERNELS = \
	linear-algebra/kernels/trisolv/trisolv.c \
	linear-algebra/solvers/dynprog/dynprog.c \
	linear-algebra/solvers/ludcmp/ludcmp.c \
	medley/floyd-warshall/floyd-warshall.c \
	medley/reg_detect/reg_detect.c \
	stencils/adi/adi.c \
	stencils/fdtd-2d/fdtd-2d.c \
	stencils/jacobi-1d-imper/jacobi-1d-imper.c \
	stencils/jacobi-2d-imper/jacobi-2d-imper.c \
	stencils/seidel-2d/seidel-2d.c

SOURCES := $(filter-out $(FAILING_KERNELS),$(SOURCES))

EXECUTABLES := $(patsubst %.c,$(BUILD_DIR)/%_omp,$(SOURCES))

.PHONY: all clean

all: $(EXECUTABLES)

$(BUILD_DIR)/%_omp: %.c
	@mkdir -p $(dir $@)
	$(CC) $(CFLAGS) -Iutilities -I$(dir $<) $< utilities/polybench.c -o $@ $(LDLIBS)

clean:
	rm -rf $(BUILD_DIR)

list:
	@echo "Benchmark Executables:"
	@for exe in $(EXECUTABLES); do \
		echo " - $$exe"; \
	done


check:
	@chmod +x utilities/time_benchmark.sh
	@for exe in $(EXECUTABLES); do \
		echo "========================================"; \
		echo "Benchmarking: $$exe"; \
		./utilities/time_benchmark.sh $$exe; \
		echo ""; \
	done
