# DirtyIO

## Description

__At least python 3.5 is needed to use this module__

DirtyIO was designed to ease the processing of binary data by providing an interface to break down the reading and writing process into comprehensible pieces.

## Documentation

### DirtyReader
    Interface laid over a file pointer for reading actions

- constructor(Path)

    Takes path string or a Pathlib.Path (**not a file pointer!**) as an argument and returns an instance of DirtyReader.
    Path will be checked for sanity. In case the path is not a file, an empty DirtyReader is returned.
- is_reader_valid

    Checks whether the Reader is empty or not.
- read_*(amount)

    Reads a provided amount of of chunks of the underlying file and returns it as a byte, if amount=1,
    or a byte array if amount>1.
    The chunk size ranges from singular bytes to qwords (4 words aka 8 bytes).
    A list of the names for the availabe chunk sizes can be found below.
- read_*_ints(amount,be=True,sig=False)

    Reads a provided amount of of chunks of the underlying file and returns it as an int, if amount=1,
    or an int array if amount>1. The be flag (whether the bytes shall be interpreted as big endian)
    and the sig flag (whether the bytes shall be interpreted as a signed number) can be specified, but
    are optional.
    The chunk size ranges from singular bytes to qwords (4 words aka 8 bytes).
    A list of the names for the availabe chunk sizes can be found below.
- read_nt_string()

    Reads and returns a null terminated string. In case the End of file has been reached before the string 
    gets terminated, an error is thrown and the file is reverted to the previous state.
- pad_out() 

    Sets the pointer to the nearest word (4 byte) border. The 1 to 3 bytes are seen as padding and are omitted.
- skip_*()

    Skip one chunk of the underlying file omitting it in the process.
    The chunk size ranges from singular bytes to qwords (4 words aka 8 bytes).
    A list of the names for the availabe chunk sizes can be found below.
- rebase(Path)

    close underlying file and switch to another. Returns whether the switch was successful.
    If not, the state is not changed, aka the old file is still open.
- passthrough functions

    Since the underlying file pointer shall not be accessed for consistency reasons, there are some functions
    which are just passed through the native file pointer object.
    
     * tell()
    
         Returns the distance between current position and beginning of file in bytes.
         
     * seek(amount, mode)
        
         pushes the file pointer to a another point in the file. This function was enabled to allow custom
         reverting via tell-seek pattern. It was designed to be the last resort. Please treat it as such.
         
     * dirty_bytes(amount)
        
         returns the "dirty" byte string of specified length. This function was enabled to allow processing
         of data that should not be interpreted, e.g. non null terminating textual data. It was designed to
         be the last resort. Please treat it as such.
         
     * close
        
       closes the file and emptying the DirtyReader. Just like with regular file pointers, it is highly
       advised to use this function after everything is done.
            
### DirtyWriter
    Interface laid over a file pointer for writing actions
    
- constructor(Path)

    Takes path string or a Pathlib.Path (**not a file pointer!**) as an argument and returns an instance of 
    DirtyWriter.
    Path will be checked for sanity. In case the path is not a file, an empty DirtyWriter is returned. 
- is_writer_valid()

    Checks whether the Writer is empty or not.
- write_bytes(bytes)

    Writes a byte(string) or byte array to the file
    
- write_ints(ints,be=true,sign=false)

    Writes an int or int array to the file. The be flag (whether the bytes shall be interpreted as big endian)
    and the sig flag (whether the bytes shall be interpreted as a signed number) can be specified, but
    are optional.
    
- write_bytes_as_nt_string(bs)

    writes the bytestring or bytearray to the file and adds a null terminator byte at the end.
    
- rebase(Path)

    close underlying file and switch to another. Returns whether the switch was successful.
    If not, the state is not changed, aka the old file is still open.
    
- passthrough functions

    Since the underlying file pointer shall not be accessed for consistency reasons, there are some functions
    which are just passed through the native file pointer object.
    
     * close()
     
         closes the file and emptying the DirtyReader. Just like with regular file pointers, it is highly
         advised to use this function after everything is done.
            

## chunk size names
    The following is the list of names that can replace the star from the functions above:
    
  - byte    1 Byte
  - word    2 Byte (Word)
  - triplet 3 Byte
  - dword   4 Byte (Double Word)
  - quintet 5 Byte
  - sextet  6 Byte
  - septet  7 Byte
  - qword   8 Byte (Quadruple Word)
