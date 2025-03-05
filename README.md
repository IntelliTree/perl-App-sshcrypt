This script uses your ssh-agent and openssl to encrypt or decrypt files.
The encrypted file includes a copy of your ssh public key, so when you
try to decrypt it your ssh-agent will automatically prompt you to unlock
the relevant key (if not already unlocked)

The encryption mechanism uses "ssh-keygen -Y" (which consults keys from
your ssh-agent) to use your private key to sign a string of "salt" data,
then uses the signature as a password for openssl symmetric encryption.
The only way to re-create that symmetric key is to re-sign the same salt
with the same pprivate key.  The salt, public key, and encryption
parameters are written into the header of the encrypted file so that you
don't need to add all these parameters to the decode command.

This allows you to store encrypted secrets on disk which can be
automatically decrypted on demand so long as your ssh-agent and private
key are available:

    some-command --secret-from <(ssh-decrypt encrypted_file)

As a reminder, you should only forward your ssh agent to servers which
you trust, because a root user of that server can access your agent and
ask it to sign things, which can then be used to decrypt these files.

This started as a direct port of [Wout Mertens' ssh-crypt bash script][1]
to add the feature of storing the encryption settings into the head of
the encrypted file.

[1]: https://gist.github.com/wmertens/c4f2c4186c04dc5442bbe3396f2c12f6
