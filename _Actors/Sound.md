---
layout: default
title: Sound
chapter: 1
section: 5
---

# Section 1.5 &mdash; Sound

A *Sound* actor can be used to load and play sound files.  It supports panning between the left/right stereo channels and is intended for single-use sound effects.

<div class="panel callout radius">
If you need to play an audio file that you want to be cleanly <strong>looped</strong>, you'll have better luck using the <code>SOUND:PlayMusicPart()</code> singleton method which is documented in <a href="{{site.baseurl}}/Singletons/SOUND.html">Section 2.2 – SOUND</a>.
</div>


*Sound* actors have three unique attributes: `SupportPan`, `SupportRateChanging`, and `IsAction`

+ **SupportPan**
	Set this to `true` if you intend to have the sound played solely through the left (for PLAYER_1) or the right (for PLAYER_2) audio channel.  This is accomplished with the `playforplayer()` method.

+ **SupportRateChanging**
	Set this to `true` if you intend to manipulate the pitch and/or speed of the underlying RageSound.

+ **IsAction**
	Set this to `true` if you are want this Sound actor to be muted as a *theme action* via **F3**+**A**.  This can be useful to keep themers sane while they are repeatedly debugging a single screen over and over again.

<span class="CodeExample-Title">SupportPan Example:</span>
{% highlight lua linenos=table %}
-- this Sound actor will play the current theme's
-- "common start" sound first for PLAYER_1, then
-- wait two seconds, and then play it for PLAYER_2

Def.Sound{
	File=THEME:GetPathS("common", "start"),
	Name="SFX_With_Pan",

	SupportPan=true,
	SupportRateChanging=true,
	IsAction=false,

	OnCommand=function(self)
		-- play the sound out of the left channel
		self:playforplayer(PLAYER_1)
		self:queuecommand("PlayAgain")
	end,
	PlayAgainCommand=function(self)
		self:sleep(2)

		-- play the sound out of the right channel
		self:playforplayer(PLAYER_2)
	end
}
{% endhighlight %}


<span class="CodeExample-Title">SupportRateChanging Example:</span>
{% highlight lua linenos=table %}
-- variables local to this file
local ragesound_file
local current_pitch = 1

-- this Sound actor will play the same audio file
-- three times sequentially, each time with a higher pitch
Def.Sound{
	File=THEME:GetPathS("common", "start"),
	Name="SFX_With_Pitch",
	SupportRateChanging=true,

	OnCommand=function(self)
		self:queuecommand("Play")
	end,
	PlayCommand=function(self)

		-- get the underlying sound file data
		ragesound_file = self:get()

		-- RageSound:pitch() take a float where
		-- 1.0 is "normal", 2.0 is twice as high as normal, etc.
		ragesound_file:pitch( current_pitch )

		-- play the sound file using both stereo channels
		self:play()

		-- increment current_pitch
		current_pitch = current_pitch + 1

		-- prevent infinite looping
		if current_pitch < 4 then
			-- sleep for two seconds and do it again
			self:sleep(2):queuecommand("Play")
		end
	end
}
{% endhighlight %}