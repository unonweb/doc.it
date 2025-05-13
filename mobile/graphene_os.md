# app store

* https://privsec.dev/posts/android/android-tips/#where-to-get-your-applications

## aurora store

* https://discuss.grapheneos.org/d/11849-spoofing-install-sources-for-non-play-store-installed-apps

Aurora Store doesn't securely retrieve apps from the Play Store since  it doesn't verify the signatures proving the apps came from the Play  Store or signed metadata, and it doesn't reduce the trusted set of  certificate authorities. This is not how the security model for  installing apps from an app store is meant to be. It shouldn't be  possible for thousands of organizations with access to a CA to intercept the connections. There's also no enforced Certificate Transparency so  it would not be possible to detect that it happened as it would be for a web site which acts as a form of deterrence, although most  organizations don't check CT logs.

Spoofing the app source for Aurora Store would be a security vulnerability in the OS rather than only in Aurora Store.

Apps with this constraint depend on Play services and the Play Store, so why not install them from the sandboxed Play Store? That's much more secure and you can still use a throwaway account. Using shared  throwaway accounts also creates potential security issues and is clearly against the terms of use.

I know this is a game of cat and mouse, but it can get rid of the minor inconvenience.

The check in Android Auto is for security and safety policy  enforcement. The check in most other apps will get replaced by the Play  Integrity API so spoofing it is meaningless.

You should just use the sandboxed Play Store as we recommend instead  of an insecure third party project that's almost certainly going to get  banned from accessing the Play Store due to breaking the account sharing rules.