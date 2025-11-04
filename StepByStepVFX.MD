# Workshop VFX

## goals
By the end of this tutorial you will know:
- How to make a simple looping visual effect
- How to use the VFX graph visual scripting language
- What all the contexts mean in the VFX graph 
- How to make an expandable Visual effect using properties

## Step 1 Set-up:
If you haven't yet, clone the URP Workshop repository.  
Navigate to Assets/Prefabs/Faucet and open it.  
You will see a simple, static and mundane faucet. We will want to make this faucet leaky through the power of visual effects, we will create a visual effect that causes droplets to drip from the tip of this faucet.
Notice the "Leaky Visual" object, this empty object has a "Visual effect" component that will contain our drip visual effect. It's "asset template" attribute is currently empty.
## Step 2 Creating the visual effect asset:
Navigate to Assets/VFX and through the Create menu, click on "Create Visual effects -> Visual effect graph".
You will see a list of default templates, but for the sake of this tutorial we will select "Minimal System".
You'll be able to name our new Visual effect. Name it "Drip" or something you prefer.
Open "Drip" by double clicking it. You will see a column of rectangles of varying colors. These are called "Contexts".
## Step 3 Applying blocks to contexts:
Contexts will contain our "Blocks" that change the "Attributes" of our particles. Attributes can be things like a particle's color, it's position, it's velocity, it's alpha, it's scale, etc.
For now, we'll want our effect to slowly spawn a small number of droplets that fall down due to gravity, and disappear after a while.
### "Slowly spawn-"
Navigate to the top of the column of rectangles to the context called "Spawn". You can do this by dragging with middle click or ALT. Scrolling will zoom in or out.
Right click the spawn context, and click on "Create Block".
In the popup, Navigate to "Spawn/Constant Spawn Rate". You can do this with your cursor or by typing in the search bar.
The "Rate" attribute represents how quickly the spawn context spawns new particles. By default it spawns 10 particles per second. 
10 is a bit much, reduce this to 1 particle per second.
### "-A small number of-"
Navigate to the "Initialise" context just below "Spawn". This contains a block by default that dictates how many particles can exist simultaneously for this effect.
A particle can only be spawned by the spawn context if the effect is under it's capacity. A 65th particle will not spawn if the capacity is 64 and there are already 64 particles spawned.
Again, 64 is fine, but let's turn this down to 10. Having a high capacity is often times a bad idea for performance reasons.
The initialise context is called every time a particle is spawned. The blocks in this context will dictate things that will stay the same throughout a particle's life, like it's spawn location, it's total lifetime, or it's rotation.
### "-Droplets-"
Let's skip to the very end of the column to the "Output" context. By default this is set to render a quad without a texture. A quad is a very lightweight plane of two polygons.
Our output context will determine the way our particles look. It will be called every rendered frame to do visual processing.
Let's set this output texture to something we can see. Set it to the "Droplet" texture included with this project under Assets/Textures/Droplet.
If we go back to the faucet's prefab, and set the Leaky Visual's Visual effect component's Asset template to "Drip" it will begin to play our unfinished effect.
If we zoom in on the end you will now see it has spawned particles with the droplet texture. 
It's a little small, isn't it? If we go back to the effect graph and add a "Scale" block under "Output" we can resize it to something bigger.
An orient camera plane block in output will always make your particles face the camera. This way your quad particles will always look proper, and you won't be able to see their thin flat side in-game.
### "-That fall down due to gravity-"
Let's make our particles move. The Update context in our graph is called every frame to handle dynamic aspects of our particles, like movement or things that have to happen over time.
If we add a "Gravity" block to our update context our particles will fall forever.
If we navigate to our prefab again you might be able to see them move for a second, and then never come back. You can retrigger this through the visual effect gizmo. Why is that?
### "-And disappear after a while"
If we recall from earlier, our particles can never exceed our capacity. Currently our capacity is 10, so our effect will spawn 10 particles and then be prohibited from spawning any more.
Usually particles have a short lifespan to despawn them after they have done their thing, currently our particles have none so they will linger for very long.
In our initialise particle context, let's add a block called "Lifetime" and let's set it to 3. Our droplets will only live for 3 seconds now.
Our particle now looks how we'd want it to! But there are still things to be tweaked.
## Step 4 Using properties:
If we open a scene with the faucet in it, like Scenes/FaucetScene, we'll see that our particles are completely unbothered by the environment. This is not good.
We'd like our particles to despawn when they hit the sink, but this is actually more difficult than you might expect. We'll need to do this through a shortcut like lowering the lifetime or adding a killplane, but we'd want this to look right for all three faucet tables. We can do this through properties.
A visual effect's property is similar to a serialised field in a script. It can be a different value for every instance of the effect and can therefore be used to differentiate our effects for our different faucets.
Go back to the visual effect graph, and click the plus sign in the top left. Navigate to property and then click plane. Let's name our new property "floorHeight".
We'll now add a new block to our update context called "Kill Shape Plane".
Click the little white notch on your kill shape block and drag it to the left. In the popup, select Property/Floor Height.
Your visual effect will now have a property representing a plane, that is different for every instance of the effect, and if your particles collide with this plane they will despawn.
Go back to the scene view of the faucet scene, and select one of the faucet's "Leaky visual" object in the object hierarchy. In the "Gizmos" dropdown, you can now select your floor height property, and adjust its height using the scene view's movement tools.
Repeat this for the other two faucets. Notice how their properties are all distinct from eachother. Designing your visual effects like this will be much appreciated by the level designers.
## Bonus Step Optional tweaks:
In real life, leaky faucets don't leak in such a constant manner. If you replace your "constant spawn rate" with "variable spawn rate", it will drip in a more random manner. Tweak this block's numbers until it looks right to you.
Adding "Set positionshape circle" to the "Initialise" context will make the drops spawn in a random position. Tweak this until it looks right, maybe even with a property?
How will you be able to give the drops a random scale within a range in the initialise context to make the drops look more normal?
Maybe your drops can look like three-dimensional spheres? Could you render your particles to a sphere mesh in output?