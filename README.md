# UHD RX Samples to RAM

Modified UHD example for capturing IQ samples to RAM before writing to storage. This prevents buffer overflows when storage write bandwidth cannot keep up with the sample rate.

## Files

- `rx_samples_to_file.cpp` - Original UHD example (streams directly to file)
- `rx_samples_to_file_opt.cpp` - Modified version (buffers to RAM, then writes to file)

## Build

```bash
g++ -std=c++11 rx_samples_to_file_opt.cpp -o rx_samples_to_file_opt $(pkg-config --cflags --libs uhd) -lboost_program_options -lpthread
```

## Usage

```bash
sudo chrt -f 99 ./rx_samples_to_file_opt --duration 25 --spb 30000 --rate 26e6 --freq 2410e6 --gain 70 --file "recording.dat"
```

### Key Options

| Option | Description |
|--------|-------------|
| `--duration` | Capture duration in seconds |
| `--rate` | Sample rate (Hz) |
| `--freq` | Center frequency (Hz) |
| `--gain` | RF gain (dB) |
| `--file` | Output filename |
| `--spb` | Samples per buffer |
| `--trig` | (WIP) Trigger threshold for signal detection |
| `--hyst` | (WIP) Hysteresis count, default 3 |

Run with `--help` for all options.

## Trigger Mode (Work in Progress)

When `--trig <threshold>` is specified, the program monitors signal magnitude every 0.5s. Recording starts when the average magnitude exceeds the threshold for `--hyst` consecutive checks (default: 3).

## Notes

- Uses `sudo chrt -f 99` for real-time scheduling priority
- Ensure sufficient RAM for the capture duration (rate x duration x 4 bytes for sc16)
