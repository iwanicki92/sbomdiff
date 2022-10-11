# SBOMDiff

SBOMDiff is a tool to compare two Software Bill of Materials (SBOM) files and
reports the differences. It supports SBOMs created in both
[SPDX](https://www.spdx.org) and [CycloneDX](https://www.cyclonedx.org) formats.

The following differences are detected:

- Package version changes
- Package licence changes
- Package removed
- Package added

## Installation

To install use the following command:

`pip install sbomdiff`

Alternatively, just clone the repo and install dependencies using the following command:

`pip install -U -r requirements.txt`

The tool requires Python 3 (3.7+). It is recommended to use a virtual python environment especially
if you are using different versions of python. `virtualenv` is a tool for setting up virtual python environments which
allows you to have all the dependencies for the tool set up in a single environment, or have different environments set
up for testing using different versions of Python.

## Usage

```
usage: sbomdiff [-h] [--sbom {auto,spdx,cyclonedx}] [--exclude-license] [-d] [-o OUTPUT_FILE] [-V] FILE1 FILE2

SBOMDiff compares two Software Bill of Materials and reports the differences.

positional arguments:
  FILE1                 first SBOM file
  FILE2                 second SBOM file

optional arguments:
  -h, --help            show this help message and exit
  -V, --version         show program's version number and exit

Input:
  --sbom {auto,spdx,cyclonedx}
                        specify type of sbom to compare (default: auto)
  --exclude-license     suppress reporting differences in the license of components

Output:
  -d, --debug           show debug information
  -o OUTPUT_FILE, --output-file OUTPUT_FILE
                        output filename (default: output to stdout)

```
						
## Operation

The `--sbom` option is used to specify the format of the SBOM files. The default is for the type and format of the SBOM to be
automatically detected based on the extension of the file name.

| SBOM Type | Version   | Extension      |Format         |
| --------- | --------- | ---------------|---------------|
| SPDX      | 2.3       | .spdx          | TagValue      |
| SPDX      | 2.3       | .spdx.rdf      | RDF           |
| SPDX      | 2.3       | .spdx.json     | JSON          |
| SPDX      | 2.3       | .spdx.yml      | YAML          |
| SPDX      | 2.3       | .spdx.yaml     | YAML          |
| SPDX      | 2.3       | .spdx.xml      | XML           |
| CycloneDX | 1.4       | .xml           | XML           |
| CycloneDX | 1.4       | .json          | JSON          |

Details of the formats for each of the supported SBOM formats are available for
[SPDX](https://spdx.dev/) and [CycloneDX](https://cyclonedx.org/).

For SPDX SBOM files, it is assumed that the name of a Package precedes the version information for the package.
Only modules with a package name and associated version information shall be processed.

The `--output-file` option is used to control the destination of the output generated by the tool. The
default is to report to the console but can be stored in a file (specified using `--output-file` option).

## Implementation Notes

The following design decisions have been made in processing the SBOM files:

1. The comparison only considers package names, package versions and licenses. Other differences are not detected.

2. It is assumed that the SBOM is valid and contains syntactically valid data. Invalid files will be silently ignored.

4. In SPDX format, the tool assumes that the name of a package is followed by the version and license of the package.

5. If there are multiple instances of a package included in the SBOM, only the first instance will be processed.

6. If a license cannot be detected, the tool uses 'NOT FOUND' as the license to be used in the difference comparison.

7. A non-zero return value indicates that differences were detected.

## Sample Output

```
[LICENSE] glibc: License changed from GPL-2.0-only to (LGPL-2.0-only OR LicenseRef-3)
[LICENSE] Saxon: License changed from Apache-2.0 to MPL-1.0
[VERSION] rich: Version changed from 11.0.0 to 12.5.1
[REMOVED] colorama: Module removed (Version 0.4.4)
[VERSION] pygments: Version changed from 2.11.2 to 2.13.0

Summary
-------
Version changes:  2
License changes:  2
Removed packages: 1
New packages:     0
```

## License

Licensed under the Apache 2.0 Licence.

## Limitations

This tool is meant to support software development and security audit functions. However the usefulness of the tool is dependent on the SBOM data
which is provided to the tool. Unfortunately, the tool is unable to determine the validity or completeness of such a SBOM file; users of the tool
are therefore reminded that they should assert the quality of any data which is provided to the tool.

## Feedback and Contributions

Bugs and feature requests can be made via GitHub Issues.