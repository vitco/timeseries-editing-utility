```
TSDUMP                  TIMESERIES CONVERSION                          TSDUMP
TSGEN                                                                  TSGEN

NAME
	tsdump  -- converts a binary timeseries file into ascii text
	tsgen   -- converts a text file into a binary timeseries file

SYNOPSYS
	tsdump [-h] binary_file text_file
	tsgen text_file binary_file

DESCRIPTION
	The tsdump and tsgen utilities convert between a binary Time Series
	file and an ASCII text equivalent, like what ncdump and ncgen do
	for NetCDF files.

	The tsdump utility generates a text file with a format that can be
	converted back into a valid binary time series file. The text file
	can be modified using a text editor.

OPTIONS
	The tsdump utility supports one option:
	-h	converts only the header information

BACKGROUND
	These utilities were written for and tested with time series file
	format version 2.00, generated by the program SeaSondeAcquisition,
	part of the Radial Suite product for the SeaSonde instrument
	manufactured by Codar Ocean Systems in Mountain View, California.

	The rationale for developing these utilities stems from a problem
	where a single time series file can contain sweep data from two
	different receiver configurations, making it impossible to correctly
	process the data. When the data is collected during an antenna pattern
	measurement, the importance of being able to process all the useful
	data makes it worth having a tool with which to modify the timeseries
	files. The problem is particularly easy to create, you simply turn
	on time series logging and then change the receiver configuration.
	The resulting Time Series file will contain one header and sweep data
	from two different receiver configurations.

EXAMPLE
	You've got a binary timeseries file with unwanted sweeps. To delete
	the unwanted sweeps from the timeseries file, first convert it to an
	equivalent text file:

	  ./tsdump Lvl_PAFS_2018_02_28_230056.ts Lvl_PAFS_2018_02_28_230056.txt

	Edit the text file using a text editor. Locate the header block called
	'swep' containing these lines (the values below are only an example):

	sweepbandwidth:-25733.91324595400874386542
	sweeprate:1.00000000000000000000

	Change them to read:

	sweepbandwidth:-49629.68983148273400729522
	sweeprate:2.00000000000000000000

	Then browse through the 'BODY' blocks of sweep data to locate and
	delete the unwanted sweep sets. Each sweep set consists of 7 blocks,
	labelled 'gtag', 'atag', 'indx' and 'scal', followed by 3 blocks
	labelled 'alvl', one for each of the three antennas. Locate the 'alvl'
	blocks that contain unusually small amplitudes, caused by a change in
	receiver configuration. Delete all 7 blocks of each sweep set between
	the 'BODY' block and the desired receiver configuration.

	Convert the modified text file into a binary timeseries file:

	  ./tsgen Lvl_PAFS_2018_02_28_230056.txt Lvl_PAFS_2018_02_28_230056_1.ts

	Process the timeseries file as normal.

EXIT STATUS
	The tsdump and tsgen utilities exit 0 on success, 1 on error.

COMPILING
	The program can be compiled from source using any C compiler (tested
	with LLVM version 9.0.0 from Apple) and the resulting executable can
	be called either tsdump or tsgen. The program tsgen can be an identical
	copy of the tsdump executable or a link to it. The programs each behave
	according to their given file name.

	  cc ts.c -o tsdump && cp tsdump tsgen

BUGS
	The documentation for "SeaSonde Radial Site Release 6 Time Series
	File Format", dated April 19, 2009 contains the information on
	which these utilities were based but the following differences were
	noted between the documentation and contents of time series files.

	1. The big-endian file header key is "AQVL", not "AQLV" as stated.
	2. The 'sign' block's elements SiteCode and FileType are inverted.
	3. The blocks 'gtag' and 'atag' are undocumented.

	Lastly, the behavior whereby SeaSondeAcquisition continues to write
	sweep blocks to the same time series file without a new header after
	a change in receiver configuration could be considered a bug.

AUTHOR
	Marcel Losekoot, Bodega Marine Laboratory, UC Davis, April 2018.

END
```
