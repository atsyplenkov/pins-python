# pins
<a href="https://rstudio.github.io/pins-python/"><img src="docs/logo.png" align="right" height="138" /></a>

The pins package publishes data, models, and other Python objects,
making it easy to share them across projects and with your colleagues.
You can pin objects to a variety of pin *boards*, including folders (to
share on a networked drive or with services like DropBox), Posit
Connect, Amazon S3, and Google Cloud Storage. Pins can be automatically
versioned, making it straightforward to track changes, re-run analyses
on historical data, and undo mistakes.

See the [documentation](https://rstudio.github.io/pins-python) for
getting started.

You can use pins from R as well as Python. For example, you can use one
language to read a pin created with the other. Learn more about [pins
for R](https://pins.rstudio.com).

## Installation

You can install the released version of vetiver from
[PyPI](https://pypi.org/project/pins/):

``` shell
python -m pip install pins
```

And the development version from
[GitHub](https://github.com/rstudio/pins-python) with:

``` shell
python -m pip install git+https://github.com/rstudio/pins-python
```

## Usage

To use the pins package, you must first create a pin board. A good place
to start is `board_folder()`, which stores pins in a directory you
specify. Here I’ll use a special version of `board_folder()` called
`board_temp()` which creates a temporary board that’s automatically
deleted when your Python script or notebook session ends. This is great
for examples, but obviously you shouldn’t use it for real work!

``` python
import pins
from pins.data import mtcars

board = pins.board_temp()
```

You can “pin” (save) data to a board with the `.pin_write()` method. It
requires three arguments: an object, a name, and a pin type:

``` python
board.pin_write(mtcars.head(), "mtcars", type="csv")
```

    Writing pin:
    Name: 'mtcars'
    Version: 20230523T115348Z-120a5

    Meta(title='mtcars: a pinned 5 x 11 DataFrame', description=None, created='20230523T115348Z', pin_hash='120a54f7e0818041', file='mtcars.csv', file_size=249, type='csv', api_version=1, version=Version(created=datetime.datetime(2023, 5, 23, 11, 53, 48, 555797), hash='120a54f7e0818041'), tags=None, name='mtcars', user={}, local={})

Above, we saved the data as a CSV, but depending on what you’re saving
and who else you want to read it, you might use the `type` argument to
instead save it as a `joblib`, `parquet`, or `json` file.

You can later retrieve the pinned data with `.pin_read()`:

``` python
board.pin_read("mtcars")
```

        mpg  cyl   disp   hp  drat     wt   qsec  vs  am  gear  carb
    0  21.0    6  160.0  110  3.90  2.620  16.46   0   1     4     4
    1  21.0    6  160.0  110  3.90  2.875  17.02   0   1     4     4
    2  22.8    4  108.0   93  3.85  2.320  18.61   1   1     4     1
    3  21.4    6  258.0  110  3.08  3.215  19.44   1   0     3     1
    4  18.7    8  360.0  175  3.15  3.440  17.02   0   0     3     2

A board on your computer is good place to start, but the real power of
pins comes when you use a board that’s shared with multiple people. To
get started, you can use `board_folder()` with a directory on a shared
drive or in DropBox, or if you use [Posit
Connect](https://posit.co/products/enterprise/connect/) you can use
`board_connect()`:

``` python
# Note that this uses one approach to connecting,
# the environment variables CONNECT_SERVER and CONNECT_API_KEY

board = pins.board_connect()
board.pin_write(tidy_sales_data, "hadley/sales-summary", type="csv")
```

Then, someone else (or an automated report) can read and use your pin:

``` python
board = board_connect()
board.pin_read("hadley/sales-summary")
```

You can easily control who gets to access the data using the Posit
Connect permissions pane.

The pins package also includes boards that allow you to share data on
services like Amazon’s S3 (`board_s3()`), Google Cloud Storage
(`board_gcs()`), and Azure blob storage (`board_azure()`).

## Contributing

- This project is released with a [Contributor Code of
  Conduct](https://www.contributor-covenant.org/version/2/1/CODE_OF_CONDUCT.html).
  By contributing to this project, you agree to abide by its terms.

- If you think you have encountered a bug, please [submit an
  issue](https://github.com/rstudio/pins-python/issues).
