# CLR-DRAM (Capacity-Latency-Reconfigurable DRAM)

This repository contains the SPICE model used in our ISCA 2020 paper

> Haocong Luo, Taha Shahroodi, Hasan Hassan, Minesh Patel, A. Giray Yaglikci, Lois Orosa, Jisung Park, and Onur Mutlu,
> **["CLR-DRAM: A Low-Cost DRAM Architecture Enabling Dynamic Capacity-Latency Trade-Off"](https://people.inf.ethz.ch/omutlu/pub/CLR-DRAM_capacity-latency-reconfigurable-DRAM_isca20.pdf)**
> *Proceedings of the [47th International Symposium on Computer Architecture](http://iscaconf.org/isca2020/) (**ISCA**)*, Valencia, Spain, June 2020.

Please cite the above work if you make use of the models provided in this repository.

The presentations of the paper are available at:

> [Slides (pptx)](https://people.inf.ethz.ch/omutlu/pub/CLR-DRAM_capacity-latency-reconfigurable-DRAM_isca20-talk.pptx) [(pdf)](https://people.inf.ethz.ch/omutlu/pub/CLR-DRAM_capacity-latency-reconfigurable-DRAM_isca20-talk.pdf)
> [Lightning Talk Slides (pptx)](https://people.inf.ethz.ch/omutlu/pub/CLR-DRAM_capacity-latency-reconfigurable-DRAM_isca20-lightning-talk.pptx) [(pdf)](https://people.inf.ethz.ch/omutlu/pub/CLR-DRAM_capacity-latency-reconfigurable-DRAM_isca20-lightning-talk.pdf)
> [Talk Video](https://www.youtube.com/watch?v=L3Y1eOF9C7U) (20 minutes)
> [Lightning Talk Video](https://www.youtube.com/watch?v=zg1RO9uaymY) (3 minutes)

## Circuit Simulation

The simulation tool we used is [LTSpice](https://www.analog.com/en/design-center/design-tools-and-calculators/ltspice-simulator.html). The transistor model (`transistor_model.pm`) used is the 22nm [PTM model](http://ptm.asu.edu). 

### Models

The repository contains the following three circuit models

* `baseline.asc` The circuit model for the baseline architecture.
* `baseline_WR.asc` The baseline model configured to simulate the process of writing an opposite value to a DRAM cell.
* `CLRDRAM-HP.asc` The circuit model for the CLR-DRAM architecture when a row is operating in high-performance mode (configured to simulate both read and write).
* `CLRDRAM-MC.asc` The circuit model for the CLR-DRAM architecture when a row is operating in max-capacity mode (configured to simulate both read and write).

The circuit parameters are based on [Rambus DRAM technology parameters]( Rambus DRAM technology parameters ), scaled to 22nm according to the ITRS roadmap with a process variation of 5%. We perform some slight hand-tuning of the parameters so that the timing parameters can approximate common DDR4 datasheet values.

For all the models, we set them to the worst case of accessing a DRAM cell.

- The cell accessed is the farthest one from the sense amplifier in a subarray. For the CLR-DRAM architecture, we probe the bitline voltages from the sense amplifier that is far from the accessed cell.
- The initial charge level in the DRAM cell prior to the access is set to a low-level (i.e., 55% $V_{DD}$). This is to model the worst case that the cell is not accessed/refreshed recently and thus suffers from charge leakage.
- The temperature is set to 85°C (The highest temperature in the normal temperature mode defined in the JEDEC DDR4 standard).

### Running the Simulation

The simulation is configured to run for 10000 iterations of Monte Carlo simulation to account for the process variation. The timing parameters are derived from the worst case of all 10000 iterations. 

The bitline voltages are probed at the nodes ``bitline0`` and ``bitline1``. The voltage at the DRAM cell is probed at the node ``mb0`` (``mb3`` for the other coupled cell for the CLR-DRAM architecture).

The threshold values we used for determining the timing parameters are listed below.

- ``tRAS`` and `tWR` (baseline architecture): 98% $V_{DD}$ at the cell.
- `tWR_CLR` (CLR-DRAM architecture high-performance mode): 2% $V_{DD}$ at the cell that stores low-level of charge.
- ``tRAS_CLR``(CLR-DRAM architecture high-performance mode): $V_{ET}$ on the bitline, where $V_{ET}$ is the voltage of the cell storing high-level of charge at `tWR_CLR` during the write process.
- ``tRCD``: $\Delta V_{bitlines}$ = 0.85V (based on previous works that set ``tRCD`` to 75% $V_{DD}$ on the bitline).
- ``tRP``: $\Delta V_{bitlines}$ = 0.05%$V_{DD}$

