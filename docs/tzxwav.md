# `tzxwav`

Reads ZX Spectum tapes from a WAV file and converts it to TZX.

`tzxwav` is very slow and is only able to convert standard speed recordings. However, its main goal is to handle poor quality recordings. It is robust against tape speed flutter, different recording levels, inverted signals and other tape related problems.

`tzxwav` is optimized for ZX Spectrum tape recordings with standard timing, and thus unable to properly convert non-standard files, like speedloaders. There are other tools for this purpose, for example `audio2tape` that comes with the [Fuse emulator](http://fuse-emulator.sourceforge.net/).

## Usage

```
tzxwav [-h] [-o TARGET] [-p] [-v] [-t {low,med,high}]
       [-T {low,med,high}] [-l {none,short,normal,long}] [-c CLOCK]
       [-s START] [-e END] [-D] file
```

* `file`: WAV file to read from. Supported is mono and stereo, 8 and 16 bit per channel, any sampling rate. Other file formats are not supported.
* `-o`, `--to`: Target file. If omitted, `stdout` is used.
* `-p`, `--progress`: Show a progress bar on `stderr`.
* `-v`, `--verbose`: Be verbose, show blocks as they are found.
* `-t`, `--treshold`: Change sound/noise ratio treshold. Default is `mid`. Try `low` if data blocks are missing or shorter than expected. Try `high` if data blocks are longer than expected.
* `-T`, `--tolerance`: Change tape speed flutter tolerance. Default is `mid`. Try `low` if the TZX file contains many useless blocks. Try `high` if you miss headers or data blocks in the TZX file, or if data blocks are shorter than expected.
* `-l`, `--leader`: Acceptable minimal leader signal length. Default is `normal`. If there are a lot of headerless blocks, or if there are blocks missing in the TZX file, it is worth a try to play with this parameter. `none` even accepts a single header pulse.
* `-s`, `--start`: Set the first frame of the WAV file to be read. If not set, the start of file is used.
* `-e`, `--end`: Set the last frame of the WAV file to be read. For technical reasons, this limit may be exceeded by a few frames. If not set, or if set out of range, the file will be read to the end.
* `-c`, `--clock`: Change reference Z80 CPU clock speed, in Hz. Default is 3500000. It is also useful for correcting a wrong playback speed. For example, if your tape was played back 5% too fast, adjust the clock to 3500000 * 5% = 3675000 to improve the results.
* `-D`, `--debug`: Show debugging output. Useful for finding out why `tzxwav` was unable to correctly read a file. Prints detected blocks and their position frame in the WAV file. If given two times, also prints detected bits and bytes. If given three times, prints detected pulse lengths (in T states) and their WAV file position. If give four times, also prints the reason why a sync or bit pulse was rejected. Attention, it will create a *lot* of useless output!
* `-h`, `--help`: Show help message and exit.

## Sampling recommendation

You will get best results if you use a sampling rate of at least 44100 Hz, 16 bit resolution per sample, and a single audio channel. I recommend to decide either on the left or right channel, depending on the quality. Do not mix down stereo to mono.

Use a proper recording level, but try to avoid clipping.

Do not apply any low pass or band pass digital filters, unless the audio quality is really bad and you get too many CRC errors.

## Example

```
tzxwav -o tape.tzx tape.wav
```

Reads the `tape.wav` file and converts it to a `tape.tzx` file.

```
tzxwav -tlow -Thigh -lshort -o tape.tzx tape.wav
```

Tries its best to read a poor quality audio file from `tape.wav`, but may also generate some useless blocks in the TZX file.
