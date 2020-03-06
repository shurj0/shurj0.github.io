---
title: First Post
date: 2020-03-06 05:01:00
---
Hey there.

I'm Shurjo, a college student. This is a blog. This blog belongs to me. I don't know why I'm starting this blog. I only know that I felt really strongly about starting a blog.

I have no idea what I'm going to be posting here, if I'm going to be posting at all to begin with. My life really isn't all that fascinating to be blogging about in the first place. Plus, I'm a proper shit writer. I could blog about my feelings or my thoughts on things, and I think I will eventually make posts like that, but that's uncharted territory for me so I'm gonna have to ease into that. We'll see as we go. 

The only really interesting thing going on in my life (staying in the dorms over spring break) is my involvement with MSU Rocketry. I don't want to get too technical on this blog very often, but recently I ran into a mathematical problem, and the solution to this problem turned out to be quite elegant and simple. And I found that quite fascinating. So I'm going to blog about it. 

First, some background:

I'm part of the avionics subteam within the rocketry team. The competition our team is participating in, Spaceport America Cup, judges performance based on a multitude of criteria. The main criteria that concerns me is that the maximum height, or 'apogee', of the rocket. The goal is to make sure the apogee is as close to 10,000 feet as possible. Overshooting that target by 500 feet is the same as being 500 feet below. 

Most amateur rockets use commercial off-the-shelf solid rocket motors to provide thrust for their rockets. These motors consist of a mixture of a fuel and an oxidizer in solid form within a casing. When the mixture is lit, combustion begins and the rocket goes bye-bye. You can easily make a solid rocket motor at home using household ingredients. I did so myself by melting together sugar and potassium nitrate (which I stole from my school's chem lab, true story, but stump remover also works) into a mixture. Of course, its simplicity brings with it some inherent drawbacks. Solid rocket motors cannot be slowed down, controlled, or stopped, once ignited. You can guess how this would be a problem in a competition where the objective is to reach a precise height. Moreover, the performance (well, specific impulse) is quite terrible when compared to its more complicated cousins.

For the competition this year, we are using a hybrid rocket engine that we designed and built in-house. In this engine, the fuel is still solid, in this case hydroxyl-terminated polybutadiene (HTPB), while the oxidizer is liquid nitrogen dioxide. The engine design is illustrated below:


![rocket](/assets/rocket.png){: .img-fluid }


The solid fuel is in the lining of the combustion chamber, while the liquid oxidizer is stored in a seperate tank. Using a valve, we can control the flow of oxidizer into the combustion chamber, and thus control the rate of combustion. This gives us the ability to reduce and cut off thrust at will. 

Of course, this *drastically* increases the complexity of the rocket and the work we put into it, but it's only rocket science :P. 

Anyways, the fact that we can control the rocket's engine is why we need an avionics team in the first place. We can't just put a rat inside the rocket and tell it when to turn the valves. That would leave me unemployed. Also rats are dumber than computers. See, we are taking on a different challenge of our own: we are building the flight computer for the rocket in-house, too. This computer reads the sensors, controls the valves, and makes sure that we dont lose the rocket in the desert like we did last year. Anyways, I digress.

Among other things, I had to implement the algorithm that shuts off the engine so the rocket reaches an apogee as close to 10,000 feet as possible. For this, the computer needs to continuously calculate the maximum height it would reach if the engine were to be cut off at that instant. Once this calculated maximum height is approximately equal to 10,000 feet, the engine is cut off, and the rocket coasts to its apogee.

Sounds pretty simple until you realize that we don't live in an ideal world, air exists (though you could argue that air is essential in an ideal world), and thus drag is a factor, especially when you reach velocities in the ballpark of 250 meters per second. 

Once the engine is cut off, the downwards force on the rocket is gravity plus the drag force (which is some constant multiplied by the square of velocity):

$$ F = -mg - Cv^2 $$

Or in other words,

$$ ma = -mg - Cv^2 $$

$$ a = -g - \frac{C}{m}v^2 $$

$$ \frac{dv}{dt} = -g - \frac{C}{m}v^2 $$

This is a differential equation in terms of velocity with respect to time. Soving this differential equation gives us the the function of velocity, and then we can solve $$ v(t) = 0 $$ to find out when the rocket reaches its peak, then integrate velocity from $$ 0 $$ to $$ t $$ to calculate the distance it covers after the engine is shut off. 

This wouldn't be a problem if I had any idea how to solve non-linear differential equations in the first place. But even if I did, I don't want to solve it myself. I want the **computer** to solve it for me. As it turns out, the former is a prerequisite for the latter, so I had to go a different route and turn it into a problem that I was actually qualified to solve. 

Turns out, we can write 

$$ ma = m\frac{dv}{dt} = m\frac{dv}{dx}\frac{dx}{dt} $$

But, 

$$ \frac{dx}{dt} = v $$

So, 

$$ m\frac{dv}{dx}\frac{dx}{dt} = mv\frac{dv}{dt} $$

And therefore, 

$$ mv\frac{dv}{dx} = -mg - Cv^2 $$

Now, we can seperate the variables:

$$ dx = -\frac{mv}{mg + Cv^2}dv $$

Integrating this equation from $$ v_0 $$ to $$ v $$, we get

$$ \int_{0}^x dx = -\int_{v_0}^v\frac{mv}{mg + Cv^2}dv $$

$$ x = \frac{m}{2C}ln\Big(\frac{mg + Cv_0^2}{mg + Cv^2}\Big) $$

We then substitute $$ v = 0 $$ into the above equation, as velocity is 0 at maximum height, to get:

$$ x_{max} = \frac{m}{2C}ln\Big(1 + \frac{Cv_0^2}{mg}\Big) $$

Then, in order to calculate the max height the rocket would reach if the engine were to be shut off at that point in time, we just use the above equation, replacing $$ v_0 $$ with the velocity read from the rocket's accelerometer. Testing this solution against data from a simulated launch, the algorithm predicts the maximum altitude within a more than reasonable margin of error. 

With this solution, the computer doesn't have to spend nearly as much time solving complicated differential equations. All it needs to do is plug in the value at each time step. Literally. 

Ok now I need to shower and sleep. Its 3 in the morning and I've been typing out this pointless post for way too fucking long. If you wanna a life lesson to take away from this post, let it be this: 

Don't stay sober over spring break, you might start blogging.