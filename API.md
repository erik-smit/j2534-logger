# Introduction #

j2534-logger is a shim DLL that sits between a J2534-application and a J2534-device. It records debugging information about the API calls to a circular memory-buffer and can write that information to a file.

# Details #

This library exports all standard PassThru functions, plus a few new functions that control the logging.

## PassThruLoadLibrary ##
This function causes the shim to load a J2534 DLL. This function must be called one time before any other function with the exception of [PassThruWriteToLog](API#PassThruWriteToLog.md), [PassThruSaveLog](API#PassThruSaveLog.md), and [PassThruGetLastError](API#PassThruGetLastError.md). Any other function called before a successful call to [PassThruLoadLibrary](API#PassThruLoadLibrary.md) will return an error. If the DLL exists and was loaded successfully, a value of STATUS\_NOERROR is returned.

### C/C++ Prototype ###
```
extern "C" long WINAPI PassThruLoadLibrary(
   char *szFunctionLibrary
);
```

**szFunctionLibrary**: Pointer to FunctionLibrary string. This must contain a path to a v04.04 J2534 DLL.

### Return Values ###
| **Definition** | **Description** |
|:---------------|:----------------|
| STATUS\_NOERROR | Function call successful |
| ERR\_NULL\_POINTER | NULL pointer supplied where a valid pointer is required |
| ERR\_FAILED    | Undefined error, use [PassThruGetLastError](API#PassThruGetLastError.md) for text description |

## PassThruWriteToLog ##
This function write a string into the debug log. There is no limit to the length of this string, but a single line (with no newline character) is recommended because the text will output like:

> 0.00s  'Vehicle identified, ready to reflash!'

Two versions of this function exist, one accepts an ANSI string and the other accepts a UNICODE string. The log uses UNICODE for the internal memory-buffer and will save to a UTF-8 file.

### C/C++ Prototype ###
```
extern "C" long WINAPI PassThruWriteToLogA(
   char *szMsg
);
```
**szMsg**: Pointer to an ANSI string.

```
extern "C" long WINAPI PassThruWriteToLogW(
   wchar_t *szMsg
);
```
**szMsg**: Pointer to a UNICODE string.

### Return Values ###
| **Definition** | **Description** |
|:---------------|:----------------|
| STATUS\_NOERROR | Function call successful |
| ERR\_NULL\_POINTER | NULL pointer supplied where a valid pointer is required |
| ERR\_FAILED    | Undefined error, use [PassThruGetLastError](API#PassThruGetLastError.md) for text description |

## PassThruSaveLog ##
This function saves the debug log from an internal circular memory-buffer to a UTF-8 encoded file. If the function is successful a value of STATUS\_NOERROR is returned and the memory-buffer is cleared.

This function may be called at any time, even without a J2534 DLL loaded.

### C/C++ Prototype ###
```
extern "C" long WINAPI PassThruSaveLog(
   char *szFilename
);
```

**szFilename**: Pointer to the output filename string.

### Return Values ###
| **Definition** | **Description** |
|:---------------|:----------------|
| STATUS\_NOERROR | Function call successful |
| ERR\_NULL\_POINTER | NULL pointer supplied where a valid pointer is required |
| ERR\_FAILED    | Undefined error, use [PassThruGetLastError](API#PassThruGetLastError.md) for text description |

## PassThruUnloadLibrary ##
This function causes the shim to unload the J2534 DLL. After this call, any function except [PassThruWriteToLog](API#PassThruWriteToLog.md), [PassThruSaveLog](API#PassThruSaveLog.md), [PassThruGetLastEror](API#PassThruGetLastEror.md), and [PassThruLoadLibrary](API#PassThruLoadLibrary.md) will return ERR\_FAILED. If the function is successful a value of STATUS\_NOERROR is returned.

### C/C++ Prototype ###
```
extern "C" long WINAPI PassThruUnloadLibrary();
```

### Return Values ###
| **Definition** | **Description** |
|:---------------|:----------------|
| STATUS\_NOERROR | Function call successful |
| ERR\_FAILED    | Undefined error, use [PassThruGetLastError](API#PassThruGetLastError.md) for text description |

## PassThruGetLastError ##
This function returns the text string description for an error detected during the last function call. Like SAE J2534-1, the buffer pointed to by pErrorDescription is allocated by the application and must be at least eighty (80) characters. The function can be called at any time, even before calling [PassThruLoadLibrary](API#PassThruLoadLibrary.md) or after calling [PassThruUnloadLibrary](API#PassThruUnloadLibrary.md).

The string describes an internal error if the shim has not loaded a J2534 DLL, if a DLL is loaded but does not provide an export for the last function call, or if the last function call was [PassThruLoadLibrary](API#PassThruLoadLibrary.md), [PassThruUnloadLibrary](API#PassThruUnloadLibrary.md), [PassThruWriteToLog](API#PassThruWriteToLog.md), or [PassThruSaveLog](API#PassThruSaveLog.md). In all other cases string comes from the J2534 DLL's function PassThruGetLastError.

### C/C++ Prototype ###
```
extern “C” long WINAPI PassThruGetLastError(
   char *pErrorDescription
)
```

**pErrorDescription**: Pointer to error description string.

### Return Values ###
| **Definition** | **Description** |
|:---------------|:----------------|
| STATUS\_NOERROR | Function call successful |
| ERR\_NULL\_POINTER | NULL pointer supplied where a valid pointer is required |