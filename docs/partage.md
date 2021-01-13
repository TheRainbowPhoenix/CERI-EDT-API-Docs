# Trivia
- `Partage` is using the [Zimbra 8.8.8_GA_1703 (build 20180523093409)](https://files.zimbra.com/docs/soap_api/8.8.8/api-reference/index.html) that provides both REST and SOAP
- Among my pre-tests, I've managed to only use the SOAP efficiently. 
- My tests have been posted [here](https://github.com/TheRainbowPhoenix/PartagePegado) (Don't be scared by the crypto stuff but I hate having my password plain-text)

# Getting started
The [python-zimbra](https://github.com/Zimbra-Community/python-zimbra) can provide an easy way to communicate with the Zimbra SOAP backend. I recommend using it.
The SOAP endpoint is located at `https://partage.univ-avignon.fr/service/soap`. Logging into it using your partage username / password will simply bypass the CAS auth so it'll be fine.

In my [demo](https://github.com/TheRainbowPhoenix/PartagePegado/blob/main/demo.py), you'll find some easy way to get your inbox and send a simple html-powered message.

*Disclaimer*:
Since I've received a lot of spam containing fancy HTML, I'd require you to NOT use this to spam any account. This is simply and purely used as documentation for any CERI student that would like to send and receive messages and have no idea how to do so. 
