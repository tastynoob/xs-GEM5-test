# xs GEM5 test

### prepare
```sh
# clone gem5
git clone https://github.com/OpenXiangShan/GEM5.git && cd GEM5
# build DRAMsim3
cd ext/dramsim3
git clone https://github.com/umd-memsys/DRAMsim3.git && cd DRAMsim3
mkdir build && cd build && cmake .. && make
cd ../../../../
# build gem5
scons build/RISCV/gem5.opt -j32 --gold-link

# download difftest reference
wget https://github.com/OpenXiangShan/GEM5/releases/download/2023Oct27/riscv64-nemu-interpreter-231008.so
```

### run a simple test 
```sh
./build/RISCV/gem5.opt \
./configs/example/fs.py \
--xiangshan-system --cpu-type=DerivO3CPU \
--mem-size=8GB \
--caches --cacheline_size=64 \
--l1i_size=64kB --l1i_assoc=8 \
--l1d_size=64kB --l1d_assoc=8 \
--l1d-hwp-type=XSCompositePrefetcher --short-stride-thres=0 \
--l2cache --l2_size=1MB --l2_assoc=8 \
--l3cache --l3_size=16MB --l3_assoc=16 \
--l1-to-l2-pf-hint --l2-hwp-type=WorkerPrefetcher \
--l2-to-l3-pf-hint --l3-hwp-type=WorkerPrefetcher \
--mem-type=DRAMsim3 --dramsim3-ini=ext/dramsim3/xiangshan_configs/xiangshan_DDR4_8Gb_x8_3200_2ch.ini \
--bp-type=DecoupledBPUWithFTB --enable-loop-predictor \
--enable-difftest --difftest-ref-so=./riscv64-nemu-interpreter-231008.so \
--raw-cpt --generic-rv-cpt=../ready-to-run/matrix-riscv64-xs.bin
```