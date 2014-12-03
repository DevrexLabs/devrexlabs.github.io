---
title: Do not store plaintext passwords please!
layout: layout
comments: true
excerpt: Example code showing how to manage passwords by hashing with salt and integrating into your OrigoDB solution.
---

# {{page.title}}

Never store passwords or move them across the wire in plaintext. In OrigoDB this means

* Don't put passwords in the model
* Don't put passwords in any commands
* Don't pass passwords to command methods when using proxy

Passwords in the model will be included in snapshots while commands, both explicit and implicit in the case of proxying, will be written to the journal.

## Password hashing
The common way to 'remember' passwords is by computing and storing a hash, preferably using some salt. The following example class will help you achieve this while encapsulating all the details. To learn more check out [Hash and salt passwords in c#](http://stackoverflow.com/questions/2138429/hash-and-salt-passwords-in-c-sharp) on Stack Overflow. Feel free to copy and use the code.

{% highlight csharp %}
using System.Security.Cryptography;
using System.Linq;

[Serializable]
public class HashedPassword
{
  private readonly byte[] Salt;
  private readonly byte[] Hash;

  private const int Iterations = 10000;
  private const int SaltLength = 100;
  private const int HashLength = 100;

  public static HashedPassword Create(string plaintext)
  {
    var salt = RandomSalt();
    var hash = ComputeHash(plaintext, salt);
    var result = new HashedPassword(hash,salt);
    return result;
  }

  private HashedPassword(byte[] hash, byte[] salt)
  {
    Salt = salt;
    Hash = hash;
  }

  private static byte[] RandomSalt()
  {
    RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
    byte[] buf = new byte[SaltLength];
    rng.GetBytes(buf);
    return buf;
  }

  public bool Matches(string plaintext)
  {
    return ComputeHash(plaintext, Salt).SequenceEqual(Hash);
  }

  private static byte[] ComputeHash(string plaintext, byte[] salt)
  {
    using(var hasher = new Rfc2898DeriveBytes(plaintext, salt, Iterations))
    {
      return hasher.GetBytes(HashLength);
    }
  }
}
{% endhighlight %}

## Using the HashedPassword class in an OrigoDB data model
Here's some code showing how to use the HashedPassword class.

{% highlight csharp %}

[Serializable]
public class User
{
  public readonly string UserId;
  private HashedPassword _hashedPassword;

  public bool Authenticate(string plaintext)
  {
    return _hashedPassword.Matches(plaintext);
  }

  public void SetPasswordHash(HashedPassword hashedPassword)
  {
    _hashedPassword = hashedPassword;
  }

  public User(string userId, HashedPassword hashedPassword)
  {
    UserId = userId;
    _hashedPassword = hashedPassword;
  }
}

[Serializable]
public class MyModel : Model
{
  private SortedDictionary<string,User> _users = new SortedDictionary<string,User>();
  public User GetUserById(string userId)
  {
    User user;
    _users.TryGetValue(userId, out user);
    return user;
  }

  public void AddUser(User user)
  {
    _users[user.UserId] = user;
  }

}


[Serializable]
public class ChangePasswordCommand : Command<MyModel>
{
  public readonly string UserId;
  private readonly HashedPassword _hashedPassword;

  public ChangePasswordCommand(string userId, string hashedPassword)
  {
    UserId = userId;
    _hashedPassword = hashedPassword;
  }

  public void Execute(MyModel model)
  {
    var user = model.GetUserById(UserId);
    if (user != null) user.SetPasswordHash(_hashedPassword);
  }

}

{% endhighlight %}

## Calling from the service/application layer
Here's an example ASP.NET MVC Controller method calling into Origo. The plain text password is transmitted using http POST from the web page, make sure you are using SSL. It only exists in memory for the duration of the request.

{% highlight csharp %}

[HttpPost]
public ActionResult ChangePassword(ChangePasswordModel model)
{

  User currentUser = Session["user"];
  if (currentUser.Authenticate(model.OldPassword))
  {
    var hashedPassword = HashedPassword.Create(model.NewPassword);
    var command = new ChangePasswordCommand(currentUser.UserId, hashedPassword);
    Engine.For<MyModel>().Execute(command);
    currentUser.SetHashedPassword(hashedPassword);
  }
  return View();
}

{% endhighlight %}
