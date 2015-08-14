# vefa-innlevering - shared component for creating ASiC-based eSubmission messages

The purpose of this component is to create valid eSubmission messages suitable for
the PEPPOL network.

There are three modes of operation:

  1. Create ASiC archive based upon the contents of an SBDH in a XML file. This
     is the recommended way of doing it.

         java -jar vefa-innlevering.jar -sbdh <filename> -ks <keystore> -ksp <keystore password> \
             -pp <private key password>

     You should end up with an ASiC archive looking like this (the order is not important):

          asice-archive:
             |-- mimetype
             |-- sample-readme.txt
             |-- sbdh.xml
             |-- trdm090-submit-tender-sample.xml
             |
             \---META-INF
                 |-- asicmanifest.xml
                 |-- manifest.xml
                 |-- signature-2009bda5-f28e-4302-8350-fb7589b83bd7.p7s


  1. Create ASiC archive manually, specifying each and every file to be included.

        java -jar vefa-innlevering.jar -sbdh <filename> -bis <filename> -a <attachment> \
             -ks <keystorefile> -ksp <keystore password> -pp <private key password>

  1. Scan a directory and create ASiC archives for each SBDH file found.

        Not implemented yet.

The process of creating an eSubmission message in ASiC archives goes like this:

1. Create the ASiC (ZIP) file.
1. Copy all files into ASiC archive, while calculating the checksums as we go along.
1. Create the `META-INF/asicmanifest.xml` file, which describes each file added
1. Sign the `asicmanifest.xml` file using the private key and certificate supplied.
1. Close the ASiC (ZIP) file.


## Example command lines

The examples below assumes that the Java jar file `vefa-innlevering.jar`, holding the complete distribution, i.e. with
dependencies, is available in the current directory.

Furthermore, the `java` command must be available.

### Create ASiC archive based on the contents of SBDH

Creates a sample ASiC archive containing the test documents provided in this implementation.
The signature is created using the supplied test certificates.

    java-jar vefa-innlevering.jar -sbdh test -keystore test

The sbdh file is included as part of the signed contents of the ASiC archive.

### Creating a single ASiC archive using the supplied test resources
`vefa-innlevering` comes with some test files and a test certificate and henceforth, we courteously supply you
with the ability to create test messages:

    # Creates ASiC archive named 'vefa-innlevering.asice' in current directory
    java -jar vefa-innlevering.jar -bis test -keystore test

### Creating a single ASiC archive
This is how you create an ASiC archive from the file `trdm090.xml`:
    # Input files: trdm090 (main document) and brochure.pdf (attachment)
    # Output: message42.asice

    java -jar vefa-innlevering.jar -o message42.asice -bis trdm090.xml -a brochure.pdf \
         -keystore keystore.jks -ksp keystore_password -pkp private_key_password

### Creating multiple ASiC archives
A directory is scanned, and for each XML file found, an ASiC archive is created.

Note! This feature has not yet been implemented.

### Iterates all XML files in directory_name and creates corresponding messages

    java -jar vefa-innlevering.jar -d directory_name -out directory_name \
         -keystore keystore.jks -ksp keystore_password -pkp private_key_password
