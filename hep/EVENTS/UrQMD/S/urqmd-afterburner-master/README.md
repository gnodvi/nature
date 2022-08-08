# urqmd-afterburner

UrQMD, tailored for use a hadronic afterburner in hybrid hydro+cascade models.

## Attribution

UrQMD is a large, complex model written by many contributors.
Please see the UrQMD website http://urqmd.org and the included license file for more information.

## Usage

This repository contains

- The latest public version of UrQMD (3.4), not including the large equation of state tables, which are only used for UrQMD's built-in hydro mode.
- The OSCAR to UrQMD converter (`osc2u`) written by Steffen A. Bass.
- A simple wrapper script, `afterburner`, that runs `osc2u` and `urqmd` in sequence.

Compile and install:

    mkdir build && cd build
    cmake .. [-DCMAKE_INSTALL_PREFIX=<prefix>]
    make install

This will place the compiled binaries and wrapper script in `<prefix>/bin`:

Invoke the afterburner with two arguments:

    afterburner input_file output_file

where `input_file` contains particle data (presumably produced by a Cooper-Frye hypersurface sampler) in the format described below and `output_file` is the desired filename for the final particle data.

__Note:__ Several intermediate files are created and left in the working directory:

- `urqmd.conf` - UrQMD configuration file
- `urqmd_input.dat` - the intermediate data file between `osc2u` and `urqmd`
- `tables.dat` - binary cache file generated by `urqmd`

If running the afterburner multiple times, it is recommended to reuse the same working directory so that `tables.dat` can be reused.

### Input format

Input files contain particle data for one or more events; multiple events are concatenated together.
Each event must begin with a commented header line containing the number of particles in the event

    # nparticles

The first characters of the line is ignored and the next field is read as an integer.
After the header line, the next `nparticles` lines must contain particle data fields

    ID t x y z E px py pz

i.e. the particle's PDG ID, then its position and momentum four-vectors.

Files are read using Fortran list-directed input so the format is fairly flexible.
Numbers may be in any valid format, separated by spaces or commas.

### Output format

The default output format is easy to parse and contains only the necessary information for computing standard event-by-event observables (charged-particle multiplicity, flow cumulants, etc).

Similar to the input format, files contain one or more events concatenated together, each beginning with a commented header line and followed by particle lines.
The header contains the event number and total number of particles in the event:

    # event n particles m

Then, particle lines have columns:

    ID charge pT ET mT phi y eta

where

- `ID` is the PDG Monte Carlo particle ID
- `charge` is the electric charge
- `pT` is the magnitude of transverse momentum
- `ET` is the transverse energy (ET = E sin(θ) = E pT/p)
- `mT` is the transverse mass (mT<sup>2</sup> = m<sup>2</sup> + pT<sup>2</sup>)
- `phi` is the azimuthal angle of transverse momentum
- `y` is the rapidity
- `eta` is the pseudorapidity