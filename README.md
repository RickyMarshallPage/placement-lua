--really messy test codes

if game:GetService("RunService"):IsClient() then error("Script must be server-side in order to work; use h/ and not hl/") end
local Player,game,owner = owner,game
local RealPlayer = Player
do
	print("FE Compatibility code V2 by Mokiros")
	local RealPlayer = RealPlayer
	script.Parent = RealPlayer.Character

	--Fake event to make stuff like Mouse.KeyDown work
	local Disconnect_Function = function(this)
		this[1].Functions[this[2]] = nil
	end
	local Disconnect_Metatable = {__index={disconnect=Disconnect_Function,Disconnect=Disconnect_Function}}
	local FakeEvent_Metatable = {__index={
		Connect = function(this,f)
			local i = tostring(math.random(0,10000))
			while this.Functions[i] do
				i = tostring(math.random(0,10000))
			end
			this.Functions[i] = f
			return setmetatable({this,i},Disconnect_Metatable)
		end
	}}
	FakeEvent_Metatable.__index.connect = FakeEvent_Metatable.__index.Connect
	local function fakeEvent()
		return setmetatable({Functions={}},FakeEvent_Metatable)
	end

	--Creating fake input objects with fake variables
	local FakeMouse = {Hit=CFrame.new(),KeyUp=fakeEvent(),KeyDown=fakeEvent(),Button1Up=fakeEvent(),Button1Down=fakeEvent(),Button2Up=fakeEvent(),Button2Down=fakeEvent()}
	FakeMouse.keyUp = FakeMouse.KeyUp
	FakeMouse.keyDown = FakeMouse.KeyDown
	local UIS = {InputBegan=fakeEvent(),InputEnded=fakeEvent()}
	local CAS = {Actions={},BindAction=function(self,name,fun,touch,...)
		CAS.Actions[name] = fun and {Name=name,Function=fun,Keys={...}} or nil
	end}
	--Merged 2 functions into one by checking amount of arguments
	CAS.UnbindAction = CAS.BindAction

	--This function will trigger the events that have been :Connect()'ed
	local function TriggerEvent(self,ev,...)
		for _,f in pairs(self[ev].Functions) do
			f(...)
		end
	end
	FakeMouse.TriggerEvent = TriggerEvent
	UIS.TriggerEvent = TriggerEvent

	--Client communication
	local Event = Instance.new("RemoteEvent")
	Event.Name = "UserInput_Event"
	Event.OnServerEvent:Connect(function(plr,io)
		if plr~=RealPlayer then return end
		FakeMouse.Target = io.Target
		FakeMouse.Hit = io.Hit
		if not io.isMouse then
			local b = io.UserInputState == Enum.UserInputState.Begin
			if io.UserInputType == Enum.UserInputType.MouseButton1 then
				return FakeMouse:TriggerEvent(b and "Button1Down" or "Button1Up")
			end
			if io.UserInputType == Enum.UserInputType.MouseButton2 then
				return FakeMouse:TriggerEvent(b and "Button2Down" or "Button2Up")
			end
			for _,t in pairs(CAS.Actions) do
				for _,k in pairs(t.Keys) do
					if k==io.KeyCode then
						t.Function(t.Name,io.UserInputState,io)
					end
				end
			end
			FakeMouse:TriggerEvent(b and "KeyDown" or "KeyUp",io.KeyCode.Name:lower())
			UIS:TriggerEvent(b and "InputBegan" or "InputEnded",io,false)
		end
	end)
	Event.Parent = NLS([==[local Event = script:WaitForChild("UserInput_Event")
	local Mouse = owner:GetMouse()
	local UIS = game:GetService("UserInputService")
	local input = function(io,RobloxHandled)
		if RobloxHandled then return end
		--Since InputObject is a client-side instance, we create and pass table instead
		Event:FireServer({KeyCode=io.KeyCode,UserInputType=io.UserInputType,UserInputState=io.UserInputState,Hit=Mouse.Hit,Target=Mouse.Target})
	end
	UIS.InputBegan:Connect(input)
	UIS.InputEnded:Connect(input)
	local h,t
	--Give the server mouse data every second frame, but only if the values changed
	--If player is not moving their mouse, client won't fire events
	local HB = game:GetService("RunService").Heartbeat
	while true do
		if h~=Mouse.Hit or t~=Mouse.Target then
			h,t=Mouse.Hit,Mouse.Target
			Event:FireServer({isMouse=true,Target=t,Hit=h})
		end
		--Wait 2 frames
		for i=1,2 do
			HB:Wait()
		end
	end]==],script)

	----Sandboxed game object that allows the usage of client-side methods and services
	--Real game object
	local RealGame = game

	--Metatable for fake service
	local FakeService_Metatable = {
		__index = function(self,k)
			local s = rawget(self,"_RealService")
			if s then
				return typeof(s[k])=="function"
					and function(_,...)return s[k](s,...)end or s[k]
			end
		end,
		__newindex = function(self,k,v)
			local s = rawget(self,"_RealService")
			if s then s[k]=v end
		end
	}
	local function FakeService(t,RealService)
		t._RealService = typeof(RealService)=="string" and RealGame:GetService(RealService) or RealService
		return setmetatable(t,FakeService_Metatable)
	end

	--Fake game object
	local FakeGame = {
		GetService = function(self,s)
			return rawget(self,s) or RealGame:GetService(s)
		end,
		Players = FakeService({
			LocalPlayer = FakeService({GetMouse=function(self)return FakeMouse end},Player)
		},"Players"),
		UserInputService = FakeService(UIS,"UserInputService"),
		ContextActionService = FakeService(CAS,"ContextActionService"),
		RunService = FakeService({
			_btrs = {},
			RenderStepped = RealGame:GetService("RunService").Heartbeat,
			BindToRenderStep = function(self,name,_,fun)
				self._btrs[name] = self.Heartbeat:Connect(fun)
			end,
			UnbindFromRenderStep = function(self,name)
				self._btrs[name]:Disconnect()
			end,
		},"RunService")
	}
	rawset(FakeGame.Players,"localPlayer",FakeGame.Players.LocalPlayer)
	FakeGame.service = FakeGame.GetService
	FakeService(FakeGame,game)
	--Changing owner to fake player object to support owner:GetMouse()
	game,owner = FakeGame,FakeGame.Players.LocalPlayer
end

--[[
	Written by Tunicus
	Made 10/20/2017
	Model link: https://www.roblox.com/library/1122124371/Placement-Handler
	Youtube Video: Coming Soon (TM)
	Giving credit is optional
	
	V2 Changelog
		- Now aligns to any plane (designated by baseplate rotation)
		- Rotation tweening
		- Multi-place
		- Touch compatibility (for positioning)
		- plane.stateChanged signal for implementing custom changes during item collision or loading
		- plane:setLoading(bool loading)
		 	- when set to true, locks the model in place and prevents further placing (useful for waiting for remote function responses)
			- set to false to disable
		- plane:enable(array models, bool prealigned) *NOT COMPATIBLE WITH PREVIOUS VERSION, BE SURE TO EDIT!*
			- first argument is an array of items to place. Their positions and rotations relative to eachother designate their spacing when multi-placing
			- second argument is whether the array of items is already positioned according to the rotation of the plane
			
	Usage instructions
		This is a barebones module intended for placement handling ONLY, inventory management and alike are up to the developer. The goal here is initiate placement,
			and if the user places the item, return the location they placed it at
		
		To start with, require the module in a local script(obviously). I recommend placing it somewhere where it won't be reset on player death
			local placement = require(moduleObject)
			
		Now to create your placement object, you'll need 3 things
			Plane - Placement surfaces are designated as planes and are represented by a rectangular part
			Obstacles - A model or some other holder where all of the currently placed items are stored. Items here are expected to be models and have a primary part
			Grid Size - Size of your grid in studs, most sandbox tycoons use 3 (the plane's x and z size must be divisible by the grid size!)
		
		Once you have these three arguments, create your placement object
			local object = placement.new(plane, obstacles, grid)
			
		Now to initiate placement, you'll need an item. Items must be models, have a primary part, and their x and z size must be evenly divisible by your grid size
		For the sake of flexibility, the only properties affected during placement are Hitbox.Color, Hitbox.Transparency, Hitbox.Anchored, Hitbox.CanCollide, and the model's CFrame
		If you want other placement properties, such as the model being transparent or CanCollide false, it's your responsibility to set that prior to placement
		
		Now to initiate placement, call the enable method on your object with the model(s) as the argument in the form of an array (the model should probably be a clone)
			local onObjectPlaced = object:enable({models})
			
		You'll notice I'm storing the result as a variable, this is because the enable function returns a signal that fires whenever the item is successfully placed. In order to detect this, use the connect function,
			as you would any other signal such as Part.Touched or Player.PlayerAdded
			
				onObjectPlaced:connect(function(array CFrames) -- The array returns the CFrames of each of the models in the same order they were provided in the models table
					print(location)	
				end)
		
		If you kept the above code though, placement would continue. I can't stress this enough, this module is strictly for placement handling, YOU are responsible for the rest. First of all, when the user places an item,
			you might want to disable placement. 							
			
		To disable placement while it's active, do
			object:disable()
		or
			placement.disable()
			
		
		I'd imagine you actually want to place the model itself, and since you're a competent game developer that uses FilteringEnabled, you'll have to do this server sided. Here's some untested sample code to give you
			an idea of what to do
			
			SERVER SCRIPT
					local remoteEvent = Instance.new("RemoteEvent", game:GetService("ReplicatedStorage")
					remoteEvent.Name = "PlacementEvent"
					
					local itemBin = game:GetService("ReplicatedStorage").items -- folder full of all the items in the game				
					
					remoteEvent.OnServerEvent:connect(function(player, itemName, location)
						if [insert inventory check here] then
							local item = itemBin[itemName]:clone()
							item.Parent = [insert player's base model here]
							item:SetPrimaryPartCFrame(location)
						end
					end)
				
			LOCAL SCRIPT
				
					local placement = require(moduleObject)
					local object = placement.new(plane, obstacles, gridSize)
					
					local contextActionService = game:GetService("ContextActionService")
					local button = guiButton
					local textBox = textBox
					
					local itemBin = game:GetService("ReplicatedStorage").items
					local placementEvent = game:GetService("ReplicatedStorage"):WaitForChild("PlacementEvent")
					
					local placementModel = nil
					local placementActive = false
					
					local function endPlacement()
						if (placementActive) then
							object:disable()
							placementActive = false
							placementModel:Destroy()
							placementModel = nil
						end
					end				
					
					local function itemPlaced(locations)
						placementEvent:FireServer(placementModel.Name, locations[1])
						endPlacement()
					end			
					
					button.MouseButton1Click:connect(function()
						local item = itemBin:FindFirstChild(textBox.Text)
						
						if (item and not placementActive) then
							placementModel = item:clone()
							placementActive = true
							
							for _, obj in pairs(placementModel:GetChildren()) do
								if (obj:IsA("BasePart")) then
									obj.Anchored = true
									obj.CanCollide = false
									obj.Transparency = obj.Transparency + (1 - obj.Transparency) * .5
								end
							end
								
							object:enable({placementModel})
						end
					end)
					
					game:GetService("ContextActionService"):BindActionAtPriority("CancelPlacement", endPlacement, false, 10, Enum.KeyCode.X)
--]]

--[[
	CONSTANTS (FEEL FREE TO EDIT)
--]]

local INTERPOLATION = true -- whether placement smoothing is enabled
local INTERPOLATION_DAMP = .21 -- how fast smoothing is, make it a value (0, 1]
local ROTATION_SPEED = .2 -- rotation tween speed in seconds

local COLLISION_COLOR3 = BrickColor.Red().Color -- color of the hitbox when object collides
local COLLISION_TRANSPARENCY = .5 -- transparency of the hitbox when object collides

local NORMAL_COLOR3 = BrickColor.new().Color -- color of the hitbox
local NORMAL_TRANSPARENCY = .8 -- transparency of the hitbox

local LOAD_COLOR3 = Color3.fromRGB(226, 155, 64) -- color of the hitbox when loading
local LOAD_TRANSPARENCY = .6 -- transparency of the hitbox when loading

local GRID_TEXTURE = "rbxassetid://13786085" -- texture of the grid space, set to nil if you don't want a visible grid

local OVERRIDE_CONTROLS = false -- whether click to place and r to rotate are enabled
local INPUT_PRIORITY = 9 -- input priority of default controls
local TOUCH_RANGE = 30 -- range of mobile placement

local PLACEMENT_COOLDOWN = .45 -- how quickly the user can place


--[[
	DO NOT EDIT PAST THESE LINES UNLESS YOU KNOW WHAT YOU'RE DOING	
--]]

local module = {}

local player = game.Players.LocalPlayer
local mouse = owner:GetMouse()
local runService = game:GetService("RunService")
local touch = game:GetService("UserInputService").TouchEnabled

local currentPlane
local currentBase

local renderConnection = nil

local cx, cy, cz, cr = 0, 0, 0, 0
local currentObjects = {}
local currentEvent
local currentTexture
local currentPosition = Vector3.new(0, 0, 0)
local ax, az

local currentExtentsX
local currentExtentsZ
local currentYAxis
local currentXAxis
local currentZAxis
local currentAxis

local ox, oy, oz -- do
local dxx, dxy, dxz
local dzx, dzy, dzz

local min = math.min
local max = math.max
local abs = math.abs

local springVelocity = Vector3.new(0, 0, 0)
local springPosition = Vector3.new(0, 0, 0)
local tweenGoalRotation = 0 -- springs arent worth effort for rotation
local tweenStartRotation = 0
local tweenCurrentRotation = 0
local tweenAlpha = 1

local lastRenderCycle = tick()
local lastPlacement = tick()

local function round(n, to)
	return n % to ~= 0 and (n % to) > to/2 and (n + to - (n % to)) or (n - (n % to))	
end

local function project(px, py, pz)
	px, py, pz = px - ox, py - oy, pz - oz

	return px * dxx + py * dxy + pz * dxz, px * dzx + py * dzy + pz * dzz
end

local function translate(px, pz, r)
	if (r == 0) then
		return px, pz
	elseif (r == 1) then
		return -pz, px
	elseif (r == 2) then
		return -px, -pz
	elseif (r == 3) then
		return pz, -px
	end

	--  or for all angles
	--	r = r * math.pi/2
	--	return math.cos(r) * px - math.sin(r) * pz,  math.sin(r) * px + math.cos(r) * pz	
end

local function angleLerp(a, b, d) -- to avoid angle jump
	local x1, y1 = math.cos(a), math.sin(a)
	local x2, y2 = math.cos(b), math.sin(b)

	return math.atan2(y1 + (y2 - y1) * d, x1 + (x2 - x1) * d)
end

local function calculateExtents(part, cf)
	local cf = cf or part.CFrame

	local edgeA = cf * CFrame.new(-part.Size.X/2, 0, 0)
	local edgeB = cf * CFrame.new(part.Size.X/2, 0, 0)
	local edgeC = cf * CFrame.new(0, 0, part.Size.Z/2)
	local edgeD = cf * CFrame.new(0, 0, -part.Size.Z/2)

	local edgeAx, edgeAz = project(edgeA.X, edgeA.Y, edgeA.Z)
	local edgeBx, edgeBz = project(edgeB.X, edgeB.Y, edgeB.Z)
	local edgeCx, edgeCz = project(edgeC.X, edgeC.Y, edgeC.Z)
	local edgeDx, edgeDz = project(edgeD.X, edgeD.Y, edgeD.Z)

	local extentsX = max(edgeAx, edgeBx, edgeCx, edgeDx) - min(edgeAx, edgeBx, edgeCx, edgeDx)
	local extentsZ = max(edgeAz, edgeBz, edgeCz, edgeDz) - min(edgeAz, edgeBz, edgeCz, edgeDz)

	return round(extentsX, currentPlane.grid), round(extentsZ, currentPlane.grid)
end

local states = {
	neutral = 1;
	collision = 2;
	loading = 3;
}

module.states = states

local function setState(object, state, downward)
	if (object.state and (object.state == state or (object.state > state and not downward))) then
		return
	end

	object.state = state

	if (state == 1) then
		if (NORMAL_COLOR3) then
			object.model.PrimaryPart.Color = NORMAL_COLOR3
		end
		if (NORMAL_TRANSPARENCY) then
			object.model.PrimaryPart.Transparency = NORMAL_TRANSPARENCY
		end
	elseif (state == 2) then
		if (COLLISION_COLOR3) then
			object.model.PrimaryPart.Color = COLLISION_COLOR3
		end
		if (COLLISION_TRANSPARENCY) then
			object.model.PrimaryPart.Transparency = COLLISION_TRANSPARENCY
		end
	elseif (state == 3) then
		if (LOAD_COLOR3) then
			object.model.PrimaryPart.Color = LOAD_COLOR3
		end
		if (LOAD_TRANSPARENCY) then
			object.model.PrimaryPart.Transparency = LOAD_TRANSPARENCY
		end
	end

	currentPlane.stateEvent:Fire(object.model, state)
end

--[[
	status
	model
	px, pz, r
	sx, sz
--]]

local function floatEqual(a, b)
	return (math.abs(a - b) < .01)
end

local function floatGreater(a, b)
	return (a - b) > .01
end

local function floatLesser(a, b)
	return (b - a) > .01
end

local function obstacleCollision()
	local px, pz = cx, cz

	local collision = false

	for _, model in pairs(currentPlane.obstacles:GetChildren()) do
		if (model.PrimaryPart) then

			local extentsX1, extentsZ1 = calculateExtents(model.PrimaryPart)
			local x1, z1 = project(model.PrimaryPart.Position.X, model.PrimaryPart.Position.Y, model.PrimaryPart.Position.Z)			

			for i = 1, #currentObjects do
				local object = currentObjects[i]

				if (not object.collision) then
					local r = (object.r + cr) % 4			

					local x0, z0 = translate(object.px, -object.pz, cr)
					x0 = x0 + px
					z0 = z0 + pz

					local extentsX0, extentsZ0				

					if (r == 1 or r == 3) then
						extentsX0, extentsZ0 = object.sz, object.sx
					else
						extentsX0, extentsZ0 = object.sx, object.sz
					end		

					if (floatLesser(x0 - extentsX0/2, x1 + extentsX1/2) and floatGreater(x0 + extentsX0/2, x1 - extentsX1/2) and floatLesser(z0 - extentsZ0/2, z1 + extentsZ1/2) and floatGreater(z0 + extentsZ0/2, z1 - extentsZ1/2)) then
						collision = true
						object.collision = true
						setState(object, states.collision)
					end
				end
			end
		end
	end

	for i = 1, #currentObjects do
		local object = currentObjects[i]

		if (not object.collision and object.state == states.collision) then
			setState(object, states.neutral, true)
		else
			object.collision = nil
		end
	end

	return not collision
end

local function inputCapture() -- converts user inputs to 3d position, built in mobile compatibility
	local position

	if (touch) then
		local camera = workspace.CurrentCamera
		local range = TOUCH_RANGE * (camera.Focus.p - camera.CFrame.p).magnitude/5	

		position = camera.Focus.p + camera.CFrame.lookVector * (camera.CoordinateFrame.lookVector.Y/2 + .5) * range
	else
		local hit, p = workspace:FindPartOnRayWithWhitelist(Ray.new(mouse.UnitRay.Origin, mouse.UnitRay.Direction * 999), {currentPlane.base})

		if (hit) then
			position = p
		end
	end

	return position, cr
end

local resting = false

local function calc(position, rotation, force)
	if (not currentPlane or currentPlane.loading) then
		return
	end


	force = force == true

	local ux, uz

	if (position) then
		ux, uz = project(position.X, position.Y, position.Z)
	else
		ux, uz = ax or 0, az or 0
	end

	local nr = rotation or cr

	local nx = round(ux, currentPlane.grid)
	local nz = round(uz, currentPlane.grid)

	local extentsX, extentsZ = translate(currentExtentsX, currentExtentsZ, nr)
	extentsX = abs(extentsX)
	extentsZ = abs(extentsZ)

	if (floatEqual(extentsX/2 % currentPlane.grid, 0)) then
		nx = nx + currentPlane.grid/2
	end

	if (floatEqual(extentsZ/2 % currentPlane.grid, 0)) then
		nz = nz + currentPlane.grid/2
	end

	nx = nx + currentPlane.offsetX
	nz = nz + currentPlane.offsetZ

	local borderX = currentPlane.size.X/2
	local borderZ = currentPlane.size.Z/2

	ax = ux
	az = uz

	if (nx + extentsX/2 > borderX) then
		nx = nx - (nx + extentsX/2 - borderX)
	elseif (nx - extentsX/2 < -borderX) then
		nx = nx - (nx - extentsX/2 + borderX)
	end

	if (nz + extentsZ/2 > borderZ) then
		nz = nz - (nz + extentsZ/2 - borderZ)
	elseif (nz - extentsZ/2 < -borderZ) then
		nz = nz - (nz - extentsZ/2 + borderZ)
	end

	if (force or nx ~= cx or nz ~= cz or cr ~= nr) then
		resting = false
		cx, cz, cr = nx, nz, nr
		currentPosition = (Vector3.new(ox, oy, oz) + currentXAxis * nx + currentZAxis * nz) 
		obstacleCollision()
	end
end


local function render()
	if (resting) then
		return
	end

	if (INTERPOLATION) then
		local delta = 1/60

		springVelocity = (springVelocity + (currentPosition - springPosition)) * INTERPOLATION_DAMP * 60 * delta
		springPosition = springPosition + springVelocity

		local extentsX, extentsZ = translate(currentExtentsX, currentExtentsZ, cr)

		local vx, vz = 9 * springVelocity:Dot(currentXAxis)/abs(extentsX), 9 * springVelocity:Dot(currentZAxis)/abs(extentsZ)
		local r

		print("rotation", cr * math.pi/2, tweenGoalRotation)

		if (not floatEqual(tweenGoalRotation, cr * math.pi/2)) then
			tweenStartRotation = tweenCurrentRotation
			tweenGoalRotation = cr * math.pi/2
			tweenAlpha = 0
		end

		if (tweenAlpha < 1) then
			tweenAlpha = min(1, tweenAlpha + delta/ROTATION_SPEED)
			tweenCurrentRotation = angleLerp(tweenStartRotation, tweenGoalRotation, 1 - (1 - tweenAlpha)^2)
			r = tweenCurrentRotation
		else
			r = cr * math.pi/2
		end


		local effectAngle = CFrame.Angles(math.sqrt(math.abs(vz/100)) * math.sign(vz), 0, math.sqrt(math.abs(vx/100)) * math.sign(vx))

		local rotationCFrame = currentAxis * effectAngle * CFrame.Angles(0, r, 0)
		local centerCFrame = rotationCFrame + springPosition

		for i = 1, #currentObjects do
			local object = currentObjects[i]

			local x, z = object.px, object.pz			

			object.model:SetPrimaryPartCFrame(centerCFrame * CFrame.Angles(0, object.r * math.pi/2, 0) + rotationCFrame * Vector3.new(x, object.sy/2, z))
		end		

		lastRenderCycle = tick()

		if (springVelocity.magnitude < .01 and tweenAlpha == 1) then
			resting = true
		end
	else
		local rotationCFrame = currentAxis * CFrame.Angles(0, cr * math.pi/2, 0)		

		for i = 1, #currentObjects do
			local object = currentObjects[i]
			local x, z = object.px, object.pz

			object.model:SetPrimaryPartCFrame(rotationCFrame * CFrame.Angles(0, object.r * math.pi/2, 0) + rotationCFrame * Vector3.new(x, object.sy/2, z) + currentPosition)
		end

		resting = true
	end
end

local function run(display)
	local position, rotation = inputCapture()

	if (position or rotation) then
		calc(position, rotation)
		if (display) then
			render()
		end
	end
end

local function place()
	if (currentPlane and (obstacleCollision()) and (tick() - lastPlacement) >= PLACEMENT_COOLDOWN and not currentPlane.loading) then
		lastPlacement = tick()

		local modelCFrames = {}

		for i = 1, #currentObjects do
			local object = currentObjects[i]
			modelCFrames[i] = currentAxis * CFrame.Angles(0, (object.r + cr) * math.pi/2, 0) +  (currentAxis * CFrame.Angles(0, cr * math.pi/2, 0)) * Vector3.new(object.px, object.sy/2, object.pz) + currentPosition
		end

		currentEvent:Fire(modelCFrames)		
	end
end

local function rotate()
	calc(nil, (cr + 1) % 4)
	render()
end

local function inputRotate(_, userInputState, inputObject)
	if (currentPlane and userInputState == Enum.UserInputState.End) then
		rotate()
	end
end

local function inputPlace(_, userInputState, inputObject)
	if (currentPlane and userInputState == Enum.UserInputState.End) then
		place()
	end
end

local function bindInputs()
	if (not OVERRIDE_CONTROLS) then
		game:GetService("ContextActionService"):BindActionAtPriority("PlacementModule_Rotate", inputRotate, false, INPUT_PRIORITY, Enum.KeyCode.R)
		game:GetService("ContextActionService"):BindActionAtPriority("PlacementModule_Place", inputPlace, false, INPUT_PRIORITY, Enum.UserInputType.MouseButton1)
	end
end

local function unbindInputs()
	if (not OVERRIDE_CONTROLS) then
		game:GetService("ContextActionService"):UnbindAction("PlacementModule_Rotate")
		game:GetService("ContextActionService"):UnbindAction("PlacementModule_Place")
	end
end

local function enablePlacement(plane, models, prealigned)
	if (plane == currentPlane) then
		return
	elseif (currentPlane) then
		currentPlane:disable()
	end

	if (type(models) ~= "table") then
		models = {models}
	end

	lastRenderCycle = tick()

	currentPlane = plane
	currentBase = plane.base

	local planePosition = currentBase.CFrame * Vector3.new(0, currentBase.Size.Y/2, 0)	

	ox, oy, oz = planePosition.X, planePosition.Y, planePosition.Z
	currentXAxis = currentBase.CFrame.rightVector
	currentYAxis = currentBase.CFrame.upVector
	currentZAxis = currentBase.CFrame.lookVector
	currentAxis = CFrame.new(0, 0, 0, currentXAxis.X, currentYAxis.X, -currentZAxis.X, currentXAxis.Y, currentYAxis.Y, -currentZAxis.Y, currentXAxis.Z, currentYAxis.Z, -currentZAxis.Z)
	currentObjects = {}
	dxx, dxy, dxz = currentXAxis.X, currentXAxis.Y, currentXAxis.Z
	dzx, dzy, dzz = currentZAxis.X, currentZAxis.Y, currentZAxis.Z
	cx, cy, cz, cr = 0, 0, 0, 0

	springVelocity = Vector3.new(0, 0, 0)
	springPosition = Vector3.new(0, 0, 0)

	tweenAlpha = 0
	tweenCurrentRotation = 0
	tweenGoalRotation = 0
	tweenStartRotation = 0

	local position, _ = inputCapture()

	if (not position) then
		position = planePosition
	end

	springPosition = position

	do
		local extentsXMin, extentsXMax = 10e10, -10e10
		local extentsZMin, extentsZMax = 10e10, -10e10

		for i = 1, #models do
			local model = models[i]
			local object = {}
			object.model = model

			local lookVector = object.model.PrimaryPart.CFrame.lookVector	
			local theta
			local px, pz

			if (prealigned) then
				local position = object.model.PrimaryPart.CFrame * Vector3.new(0, -object.model.PrimaryPart.Size.Y/2 , 0)		
				px, pz = project(position.X, position.Y, position.Z)

				theta = math.acos(math.clamp(lookVector:Dot(currentZAxis), -1, 1))
				local cross = lookVector:Cross(currentZAxis)

				if (cross:Dot(currentYAxis) > 0) then
					theta = -theta
				end

			else
				px, pz = object.model.PrimaryPart.Position.X, object.model.PrimaryPart.Position.Z
				theta = math.atan2(lookVector.X, lookVector.Z)		
			end

			local x, z = model.PrimaryPart.Size.X, model.PrimaryPart.Size.Z

			object.r = round((theta % (2 * math.pi))/(math.pi/2), 1)

			if (object.r == 1 or object.r == 3) then
				x, z = z, x
			end

			local x1, x2 = px + x/2, px - x/2
			local z1, z2 = pz + z/2, pz - z/2	

			if (x2 < extentsXMin) then
				extentsXMin = x2
			end
			if (x1 > extentsXMax) then
				extentsXMax = x1
			end

			if (z2 < extentsZMin) then
				extentsZMin = z2
			end
			if (z1 > extentsZMax) then
				extentsZMax = z1
			end


			model.PrimaryPart.Transparency = .5
			model.PrimaryPart.Anchored = true
			model.PrimaryPart.CanCollide = false

			setState(object, plane.loading and states.loading or states.neutral)

			currentObjects[i] = object
		end

		currentExtentsX, currentExtentsZ = round(extentsXMax - extentsXMin, currentPlane.grid), round(extentsZMax - extentsZMin, currentPlane.grid)

		for i = 1, #currentObjects do
			local object = currentObjects[i]
			local px, pz

			if (prealigned) then
				local position = object.model.PrimaryPart.CFrame * Vector3.new(0, -object.model.PrimaryPart.Size.Y/2, 0)		
				px, pz = project(position.X, position.Y, position.Z)
			else
				px, pz = object.model.PrimaryPart.Position.X, object.model.PrimaryPart.Position.Z
			end

			object.px = px - (extentsXMin + extentsXMax)/2
			object.pz = (pz - (extentsZMin + extentsZMax)/2) * (prealigned and -1 or 1)

			object.sx, object.sy, object.sz = object.model.PrimaryPart.Size.X, object.model.PrimaryPart.Size.Y, object.model.PrimaryPart.Size.Z
		end
	end

	renderConnection = runService.RenderStepped:connect(run)
	module.currentPlane = currentPlane
	currentEvent = Instance.new("BindableEvent")

	if (GRID_TEXTURE) then
		currentTexture = Instance.new("Texture", currentPlane.base)
		currentTexture.Texture = GRID_TEXTURE
		currentTexture.Face = Enum.NormalId.Top
		currentTexture.StudsPerTileU = currentPlane.grid
		currentTexture.StudsPerTileV = currentPlane.grid
	end

	bindInputs()

	run(true)

	return currentEvent.Event
end

local function disablePlacement(plane)
	if (currentPlane) then
		renderConnection:disconnect()
		renderConnection = nil
		currentPlane = nil
		module.currentPlane = nil
		currentEvent:Destroy()
		currentEvent = nil	

		if (currentTexture) then
			currentTexture:Destroy()
			currentTexture = nil
		end

		for i = 1, #currentObjects do
			local object = currentObjects[i]
			object.model = nil
			currentObjects[i] = nil
		end

		unbindInputs()
	end
end

local function setLoading(plane, isLoading)
	if (plane.loading == isLoading) then
		return
	end

	plane.loading = isLoading

	if (plane == currentPlane) then	
		if (isLoading) then
			for i = 1, #currentObjects do
				setState(currentObjects[i], states.loading)
			end
		else
			for i = 1, #currentObjects do
				setState(currentObjects[i], states.neutral, true)
			end

			obstacleCollision()
		end
	end
end

module.new = function(base, obstacles, grid)
	local plane = {}
	plane.base = base
	plane.obstacles = obstacles
	plane.position = base.Position
	plane.size = base.Size

	if (math.floor(.5 + plane.size.X/grid) % 2 == 0) then
		plane.offsetX = -grid/2
	else
		plane.offsetX = 0
	end

	if (math.floor(.5 + plane.size.Z/grid) % 2 == 0) then
		plane.offsetZ = -grid/2
	else
		plane.offsetZ = 0
	end


	plane.stateEvent = Instance.new("BindableEvent")
	plane.stateChanged = plane.stateEvent.Event	

	plane.grid = grid
	plane.enable = enablePlacement
	plane.disable = disablePlacement
	plane.rotate = rotate
	plane.place = place
	plane.setLoading = setLoading

	return plane
end

module.setLoading = setLoading
module.currentPlane = false

local gridSize = 3

local itemsInWorkspace = Instance.new("Model",script)

local plane = module.new(workspace.Base, itemsInWorkspace, gridSize)



local highlight = false
local highlightedItems = {}

local placing = false
local itemTemplate = Instance.new("Model",game:GetService("ReplicatedStorage"))
local partTemplate = Instance.new("Part",itemTemplate)

itemTemplate.Name = math.random(-34786364374,99999999999)
itemTemplate.PrimaryPart = partTemplate
partTemplate.Anchored = true

local function getRandomItem()
	local item = itemTemplate:clone()
	item.PrimaryPart.Size = Vector3.new(math.random(1, 10) * gridSize, math.random(1, 10) * gridSize, math.random(1, 10) * gridSize)
	item.Parent = workspace
	return item
end

plane.stateChanged:connect(function(model, state)
	print(model, state)
end)

local function placeItems(items)
	if (placing) then
		return
	end
	
	for _, item in pairs(items) do
		item.Parent = workspace
	end
	
	placing = true	
	
	plane:enable(items, #items > 1):connect(function(itemCFrames)
		placing = false
		for i = #items, 1, -1 do
			local item = items[i]
			plane:disable()
			item:SetPrimaryPartCFrame(itemCFrames[i])
			item.Parent = workspace.items
			table.remove(items, i)
		end
	end)
end

local function isInArray(array, value)
	for i = 1, #array do
		if (array[i] == value) then
			return i
		end
	end
end

local mouse = game.Players.Localowner:GetMouse()

mouse.KeyDown:Connect(function(keyPressed)
	if keyPressed == "q" then
		if (placing) then
			return
		end
		placeItems({getRandomItem()})
	end
end)
