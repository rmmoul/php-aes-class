php-aes-class
=============

A PHP class to handle aes text and file encryption.

I wasn't able to find a satisfacty write up or class for handling both text and file encryption/decryption using aes, so I wrote my own. 

Text encryption is easy, and can be accomplished with a user provided key and iv, or by creating random ones. Be sure to save the random ones somewhere, as neither are saved by defualt (the iv is not added to the cipher text).

    include('aes.php');
    
    $crypt = new aes_encryption();
    
    $crypt->key = $crypt->rand_key();
    $crypt->iv = $crypt->rand_iv();
    
    // or to provide a passphrase and your own iv, you could do something like the
    // following, though there are certainly better ways, this works well enough
    // for testing purposes. 
    
    // $passphrase = 'ThisIsMyPassphrase';
	  // $iv = substr(md5('YourIV'.$passphrase, true), 0, 16);
	  // $key = md5($passphrase, true);
	  
    // You would pass the below when initializing the class like:
    // $crypt = new aes_encryption($key, $iv);
  
    // we base64_encode() the plain text before encryption to ensure that the 
    // plaintext length's byte size is a multiple of 16.
    $encrypted_string = $crypt->encrypt(base64_encode('this is a test'));
    echo $encrypted_string.'<br>';

    $decrypted_string = base64_decode($crypt->decrypt($encrypted_string));
    echo $decrypted_string.'<br>';
    
File encryption is similarly easy. This method encrypts files in chunks of 4096 bytes to allow php to process large files without needing to load the entire file into memory at once.

    include('aes.php');
    
    $crypt = new aes_encryption();
    
    $crypt->key = $crypt->rand_key();
    $crypt->iv = $crypt->rand_iv();
    
    $file = 'path/to/file/file.txt';
    
    $crypt->encrypt_file($file, $file.'.enc');
    
    $crypt->decrypt_file($file.'.enc', $file);

This is just my inital set up, and it could probably use some tweaks (specifically with the file encryption and the base64 handling, which is producing encrypted files about 66% larger than the original. I'd like to cut that down but will need to test out whether I can pull one of the base64 steps without messing up the sizing of the chunks that I'm using. I also need to do more testing to ensure that removing the \0 btyes with trim will not cause any edge case issues with any files, so far none of my tests on files has failed.).
