# iNaturalist Project Extractor

Extracts iNaturalist observations for a given project, writing out to CSV file.

## Getting Started

Requires Python 3.7.1 or above, although only tested with version 3.8.3.
Developed using [Poetry](https://python-poetry.org) instead of directly using
pip; a pip requirements file has been generated for those who do not wish to
install poetry.

### Poetry

From this project directory, open a PowerShell prompt and run:

```bash
poetry install
```

### Pip

From this project directory, open a PowerShell prompt and run:

```bash
python -m pip install --user virtualenv
python -m venv .env
.\.env\Scripts\Activate.ps1
pip install -r .\requirements.txt
```

## Running the Script

There are two ways to provide necessary configuration to the `__main__.py`
script: through command line arguments or through environment variables.
Environment variables, when placed into a `.env` file, can be a convenient
alternative so that you do not have to remember the syntax details and type them
in by hand each time you run the tool.

### Command Line Arguments

| Short Flag | Long Flag          | Environment Variable | Required?          | Description                                                                                                          |
| ---------- | ------------------ | -------------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------- |
| -h         | --help             | -                    | n/a                | show a help message and exit                                                                                         |
| -t         | --api-token        | INAT_API_TOKEN       | yes                | An API token acquired from https://www.inaturalist.org/users/api_token                                               |
|            | --log-level        | LOG_LEVEL            | no - default INFO  | Standard Python logging level, e.g. ERROR, WARNING, INFO, DEBUG                                                      |
| -u         | --user-name        | INAT_USER_NAME       | yes                | iNaturalist user name                                                                                                |
| -p         | --project-slug     | PROJECT_SLUG         | yes                | Slug (short name) of the project to extract                                                                          |
| -o         | --output-directory | OUTPUT_DIR           | no - default `out` | Directory name for output files                                                                                      |
| -l         | --last-id          | LAST_ID              | no - default 0     | The last observation ID from a previous download, used to start a fresh download from the next available observation |
| -i         | --input-file       | INPUT_FILE           | no                 | An input file to merge with the downloaded results                                                                   |

NOTE: please sign-in to [iNaturalist](https://www.inaturalist.org) with your
credentials, and then visit https://www.inaturalist.org/users/api_token to
acquire an API token. This token will expire after 24 hours, at which point you
will need to visit the link above to acquire a fresh token.

#### Execute With Poetry

Poetry sets up the virtual environment for you, so it is now just one command:

```bash
poetry run python . -t <paste your long API token here> -u <your username> -p <e.g. birds-of-texas>
```

Look in the `out` directory for a fresh file.

#### Execute without Poetry

From this project directory, open a PowerShell prompt and run:

```bash
.\.venv\Scripts\Activate.ps1
python . -t <paste your long API token here> -u <your username> -p <e.g. birds-of-texas>
```

The command above has the minimum required parameters using default values for
log level and output file.

### Using Environment Variables

The simplest way to use environment variables instead of the command line
arguments is by entering them into a `.env` file. Copy the `.env.example` file
and name the copy as simply `.env`. Edit it and replace the values there with
your correct values.

Then run either:

```bash
poetry run python .
```

or:

```bash
.\.venv\Scripts\Activate.ps1
python .
```

## Usage Notes

* iNaturalist's [recommended best
  practices](https://www.inaturalist.org/pages/api+recommended+practices)
  requests that users not send more than one request per second. To that end,
  this script sleeps for one second after every request.
* The script requests 200 observations at a time in order to minimize the
   number of requests
* They also ask that you not issue more than 10,000 requests per day. The
  `birds-of-texas` project has 211,024 observations as of Jan 1, 2021. Thus the
  script will need to issue 1,056 requests to retrieve all records - well below
  the 10,000 maximum. However, if downloading multiple large projects, it might
  be good to do so with one project per day to keep from being a burden on their
  systems.
* Each batch of 200 observations is written to the output file as soon as it is
  retrieved, before requesting another batch. Thus if a failure occurs, you can
  restart the process by looking at the observation id of the last line in the
  file. Then set the `last_id` argument / environment variable to this last
  value when re-running the tool. A new output file will be created, so you will
  need to concatenate the two (or more) files manually.
