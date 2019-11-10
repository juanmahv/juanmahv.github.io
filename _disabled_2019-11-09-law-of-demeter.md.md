
One of the classical as well as mainly ignored principles of software engineering is the Principle of Least Knowledge aka *Law of Demeter*. There is many academical essays in the web about this, but the main point it's not so easy to grasp in my opinion, so I'll give a shot in explaining this concept from a developer perspective.

Imagine  a class *Customer* that offers a pointer to  class *Wallet*, so for each payment, the Customer just delegates into *Wallet*.
 
````c++
class Customer 
{
public:
Customer() {
        myWallet = new Wallet();
        myWallet->addMoney(200);
 }

~Customer(){  delete myWallet; }

Wallet* getWallet() { return myWallet; }

private:
    Wallet* myWallet;
};
````

and the class Wallet takes care of the cash by itself:

````c++
class Wallet 
{
public:
    Wallet()
    :currentMoney(0)
    {}

    void addMoney(int amount) {
        currentMoney += amount;
    }

    const int howMuchMoney() {
        return currentMoney;
    }

    int retrieveMoney(int amount) {
       int moneyTaken;
       if (amount <= currentMoney) {
           moneyTaken = amount;
           currentMoney -= amount;
       }
       else {
           moneyTaken = currentMoney;
           currentMoney = 0;
       }
       return moneyTaken;
    }

private:
    int currentMoney;
};
````

For each payment, *Customer* just delegates to *Wallet*, as you see in the following lines:

````c++
int main()

{

Customer customer;
int  customerMoney = customer.getWallet()->howMuchMoney();

std::cout << "Money, " << customerMoney << "!\n"; // Money, 200!

//Buy
bool waspaid = customer.getWallet()->retrieveMoney(3);

std::cout << "paid, " << (waspaid?"yes":"no") << "!\n"; //paid, yes!
std::cout << "current Money, " << customer.getWallet()->howMuchMoney() << "!\n"; 
//current Money, 197!

}
````

So far so good. By just calling *customer.getWallet()* class Customer shifts the work to *Wallet*. As *Wallet* know how to deal with the cash flow, all is well.

Well?, well, let's say in an update, *Customer* has to deal with 2 Wallets (I'm old enough to remember the times before &euro;, when I had to carry 2 wallets around, one with Spanish Pesetas and other with German D-Marks. Fortunately not anymore).  Translating that to our code, it means the *Customer* need a second instance of *Wallet*, a situation we can code like this:

````c++
class Customer {

public:
    Customer() {
        myWallets.push_back(new Wallet());
        myWallets.push_back(new Wallet());
        myWallets[0]->addMoney(200);
        myWallets[1]->addMoney(50);
    }

~Customer(){
  for (std::vector<Wallet*>::iterator it = myWallets.begin() ; it != myWallets.end(); ++it)
     delete(*it);
}

Wallet* getWallet(unsigned int pos) {
        if (pos < 2)
            return myWallets[pos];
        else
            return NULL;
        }

private:
       std::vector<Wallet*> myWallets;
};
````

But now, the code inside Main() is broken. We need to rewrite it and tell *which Wallet* to get the money from.
````c++
int main()
{
  Customer customer;
  int customerMoney = customer.getWallet(0)->getCurrentMoney();
  std::cout << "Money, " << customerMoney << "!\n"; //Money, 200!
}
````

You can imagine that **all** places where *customer.GetWallet()* is invoked now need correction. And depending of your code, this can be a lot of work. It's even worse if *Customer* is shipped as part of a library and you have no access to the code which makes use of the *Wallet*.

This is not only a design flow, but sometimes also a security issue. You cannot assume that clients use your API in the way you expect:

````c++
int main()
{
  //Opps!
  Customer* victim = &customer;
  victim->getWallet()->retrieveMoney(victim->getWallet()->howMuchMoney());
  std::cout << "Money left?, " << victim->getWallet()->howMuchMoney() << "!\n"; 
  //Money left?, 0!
}
````

But wait a second, why should *Main()* have to choose between wallets? Why should it know that there are two?
This imply a level of knowledge that no one from outside *Customer* class should know.  What's the problem here?

* We are exposing customer’s internal structure.
* There is a high coupling between Wallet and customer’s clients.
* The relationship *has-a* between *Customer* and *Wallet* is *de facto* part of the Customer interface to the outside world.

To sum up: We are breaking the Law of Demeter.
 
 ### Law of Demeter
According to [Wikipedia](https://en.wikipedia.org/wiki/Law_of_Demeter), The Law of Demeter for functions requires that a method **m** of an object **O** may only invoke the methods of the following kinds of objects:

    O itself
    m's parameters
    Any objects created/instantiated within m
    O's direct component objects
    A global variable, accessible by O, in the scope of m

In particular, an object should avoid invoking methods of a member object returned by another method.
Demeter law set restrictions on the **Types** of objects we can talk with.
In side a method, we can only talk to *friends*:

	Myself
	Method parameters
	Objects created by the method
	Direct components of my class
	Global variables

All other Types are *strangers*. It's not allowed invoking method of an object returned by another method: The friends of my friends are strangers to me.

In the code snipped above, class *Wallet* is clearly an strange for *Main()* method.

> Note: The concept of friend is used here as in plain language. Nothing to do with the keyword *friend* in c++

### Keeping the Law
For a better maintenance, it's better to rewrite the code to abstract *Wallet* away, so *Customer* offers 2 methods: 

	HowMuchMoney
	Pay

while the *Wallet* itself remains unexposed.
````c++
class Customer {

public:
    Customer() {
        myWallet = new Wallet();
        myWallet->addMoney(200);
    }

~Customer(){  delete myWallet; }

const int howMuchMoney() {
    return myWallet->howMuchMoney();
}

int pay(int amount) {
 return myWallet->retrieveMoney(amount);
}

private:
    Wallet* myWallet;
};

class Wallet 
{
public:
    Wallet()
    :currentMoney(0)
    {}

    void addMoney(int amount) {
        currentMoney += amount;
    }

    const int howMuchMoney() {
        return currentMoney;
    }

    int retrieveMoney(int amount) {
       int moneyTaken;
       if (amount <= currentMoney) {
           moneyTaken = amount;
           currentMoney -= amount;
       }
       else {
           moneyTaken = currentMoney;
           currentMoney = 0;
       }

       return moneyTaken;
    }

private:

    int currentMoney;
};

````

### Why is this way better?
You can argue that we added more complexity in *Customer* class and some method of Customer class are just wrapper of *Wallet*'s ones. Yes, that the price of abstraction. In return, you get

* Code easier to understand: the Customer clients do not need to know about the relationship between Customer and Wallet.
* Better maintainability: If the customer decides to change the Wallet for a payment App, the customer’s client remain unaffected. also changes in the interface of the Wallet does not propagate to the clients of Customer.

But at the end, this models better the relationship in the real world. Do you handover your wallet to the shop assistant at the market when you buy your stuff? I bet you don't. Instead you handle your wallet by yourself. It's more *complex*, as you have to take the cash,  check the change, and so on, but for all of us, this effort pays off. 

So it should be in software design.
 
 #### References
 Original paper: http://www.ccs.neu.edu/research/demeter/papers/law-of-demeter/oopsla88-law-of-demeter.pdf
“The Paperboy, The Wallet,and The Law Of Demeter” . D Bock
“An Empirical Validation of the Benefits of Adhering to the Law of Demeter”.Guo, Würsch, Giger and Gall.
https://en.wikipedia.org/wiki/Law_of_Demeter
https://pragprog.com/articles/tell-dont-ask

 



