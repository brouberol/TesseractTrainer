# TesseractTrainer
by Balthazar Rouberol - [Mor Consulting](http://morconsulting.com/), [rouberol.b@gmail.com](mailto:rouberol.b@gmail.com)

TesseractTrainer is a simple Python API, taking over the tedious process of manually 
training Tesseract3, as described in the [wiki page](https://code.google.com/p/tesseract-ocr/wiki/TrainingTesseract3).

The longest part of the training process is checking the box file, generated by tesseract using a reference tif image,
as explained [here](https://code.google.com/p/tesseract-ocr/wiki/TrainingTesseract3#Make_Box_Files).
This file contains the coordinates of each character detected in the training tif. However, if Tesseract made 
some mistakes, you have to manually correct the boxfile, allowing Tesseract to "learn" from its mistakes.

TesseractTrainer allows you to skip this part, by automatically generating a tif (and the associated boxfile) using a
text and a font that you specify, thus **guaranteeing the total accuracy of the box file**.

## Dependencies

* a Unix/Linux system
* Tesseract3.x
* python 2.6+
* PIL (Python Imaging Library). Note that PIL has not yet been ported to Python3. 
* the `tiffcp` binary (included in the `bin/` directory)

## API
The `tesseract_training.py` file offers a very simple API, defined through the class `TesseractTrainer`.
This class has only 4 public methods:

* `__init__(self, text, exp_number, dictionary_name, font_name, font_size, font_path, font_properties, tessdata_path, word_list)`: returns a `TesseractTrainer` instance
* `training(self)`: performs all training operations, thus creating a `traineddata` file.
* `add_trained_data(self)`: copies the generated `traineddata` file to your `tessdata` directory 
* `clean(self)`: deletes all files generated during the training process (except for the `traineddata` one).

I'd advise you to combine this `TesseractTrainer` class with the `argparse.ArgumentParser` (and associated security checks) I defined in `__main__.py`.

## Tif generation
During the training process, a (multi-page) tif will be generated using the `lib/multipage_tif.py` module, 
from the input `text`, `font_name`, `font_size` arguments.
The result will be a tif file named `{dictionary_name}.{font_name}.exp{exp_number}.tif`.

## Usage

	usage: __main__.py [-h] 
					  --tesseract-lang TESSERACT_LANG 
					  --training-text TRAINING_TEXT 
					  --font-path FONT_PATH 
					  --font-name FONT_NAME
	                  [--experience_number EXPERIENCE_NUMBER]
	                  [--font-properties FONT_PROPERTIES] 
	                  [--font-size FONT_SIZE]
	                  [--tessdata-path TESSDATA_PATH] 
	                  [--word_list WORD_LIST]

**Tesseract training arguments**

	  -h, --help            show this help message and exit

	**Required arguments:**
	  --tesseract-lang TESSERACT_LANG, -l TESSERACT_LANG
	                        Set the tesseract language traineddata to create.
	  --training-text TRAINING_TEXT, -t TRAINING_TEXT
	                        The path of the training text.
	  --font-path FONT_PATH, -F FONT_PATH
	                        The path of TrueType/OpenType file of the used training font.
	  --font-name FONT_NAME, -n FONT_NAME
	                        The name of the used training font. No spaces.

	**Optional arguments**
	  --experience_number EXPERIENCE_NUMBER, -e EXPERIENCE_NUMBER
	                        The number of the training experience.
	                        Default value: 0
	  --font-properties FONT_PROPERTIES, -f FONT_PROPERTIES
	                        The path of a file containing font properties for a list of training fonts.
	                        Default value: ./font_properties
	  --font-size FONT_SIZE, -s FONT_SIZE
	                        The font size of the training font, in px.
	                        Default value: 25
	  --tessdata-path TESSDATA_PATH, -p TESSDATA_PATH
	                        The path of the tessdata/ directory on your filesystem.
	                        Default value: /usr/local/share/tessdata
	  --word_list WORD_LIST, -w WORD_LIST
	                        The path of a file containing a list of frequent words.
	                        Default value: None

## Example

In this example, we would like to create a `helveticanarrow` dictionary:

* using an OpenType file located at `./font/Helvetica-Narrow.otf
* the font name is set to `helveticanarrow`
* with training text located at `./text`
* the `font_properties` file is located at `./font_properties`. It contains the following line: `helveticanarrow 0 0 0 0 0`
* the experience number is set to 0
* a tif font size of 25px
* the `tessdata` directory is located at `/usr/local/share/tessdata`
* no frequent word list

The command would thus be:

	$ python __main__.py --tesseract-lang helveticanarrow --training-text ./text --font-path font/Helvetica-Narrow.otf --font-name helveticanarrow

or using the short options names:

	$ python __main__.py -l helveticanarrow -t ./text -F ./font/Helvetica-Narrow.otf -n helveticanarrow

## Remarks

* UTF-8 encoding is supported.
* If your `tessdata` directory is not writable without superuser rights, use the `sudo` command when executing your python script.
* Do not forget to describe your font properties in a file (parser default value: "font_properties"), following [these instructions](https://code.google.com/p/tesseract-ocr/wiki/TrainingTesseract3#font_properties_%28new_in_3.01%29).

## Attributions
TesseractTrainer was completed whilst working on [StrongSteam](http://strongsteam.com) for [MorConsulting](http://morconsulting.com/).
