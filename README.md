if not game:IsLoaded() then
	game.Loaded:Wait()
end

local EnableFling = true
local ClickToFling = true
local FlingOnTouchh = false

setfpscap(120)
local function FindInstance(Parent, ClassName, Name)
	for _, Instance in pairs(Parent:GetChildren()) do
		if Instance:IsA(ClassName) and Instance.Name == Name then
			return Instance
		end
	end
end

local function WaitForClass(Parent, ClassName)
	local Instance = Parent:FindFirstChildOfClass(ClassName)

	while not Instance and Parent do
		Parent.ChildAdded:Wait()
		Instance = Parent:FindFirstChildOfClass(ClassName)
	end

	return Instance
end

local function WaitForClassOfName(Parent, ...)
	local Instance = FindInstance(Parent, ...)

	while not Instance and Parent do
		Parent.ChildAdded:Wait()
		Instance = FindInstance(Parent, ...)
	end

	return Instance
end

local Fling = { }
local Aligns = { }
local Blacklist = { }
local Accessories = { }
local Attachments = { }

local Instancenew = Instance.new

local taskwait = task.wait
local taskspawn = task.spawn
local taskdefer = task.defer

local mathabs = math.abs
local mathcos = math.cos
local mathsin = math.sin
local mathrandom = math.random

local sinvalue = 0

local stringmatch = string.match

local osclock = os.clock

local tableinsert = table.insert
local tableclear = table.clear
local tablefind = table.find

local CFramenew = CFrame.new
local CFrameAngles = CFrame.Angles
local CFrameidentity = CFrame.identity

local Vector3new = Vector3.new
local Vector3zero = Vector3.zero

local Sleep = CFrameidentity
local Velocity = Vector3new(8192,8192,8192)
local Angular = 0
local Linear = 0

local Workspace = game:FindFirstChildOfClass("Workspace")
local CurrentCamera = Workspace.CurrentCamera

local Players = game:FindFirstChildOfClass("Players")

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

local PostSimulation = game:FindFirstChildOfClass("RunService").PostSimulation

local Character = LocalPlayer.Character
local CharacterClone = Instancenew("Model")

local ssdlllll = 0

local FEFolder = Instancenew("Folder",CharacterClone)
FEFolder.Name = "FE Wings Folder"

local UselessPrt = Instancenew("Part",FEFolder)
UselessPrt.Transparency = 1
UselessPrt.CanCollide = false

local FakePrt = Instancenew("Part",FEFolder)
FakePrt.Size = Vector3new(1,1,1)
FakePrt.CanCollide = false
FakePrt.Name = "Wing "..ssdlllll + 1
local FPWeld = Instancenew("Weld",FakePrt)
FPWeld.Part0 = FakePrt
FPWeld.Part1 = UselessPrt

local FakePrt2 = FakePrt:Clone()
FakePrt2.Parent = FEFolder
FakePrt2.Name = "Wing "..ssdlllll + 2
local FPWeld2 =  FakePrt2.Weld
FPWeld2.Part0 = FakePrt2
FPWeld2.Part1 = UselessPrt

local FakePrt3 = FakePrt:Clone()
FakePrt3.Parent = FEFolder
FakePrt3.Name = "Wing "..ssdlllll + 3
local FPWeld3 =  FakePrt3.Weld
FPWeld3.Part0 = FakePrt3
FPWeld3.Part1 = UselessPrt

local FakePrt4 = FakePrt:Clone()
FakePrt4.Parent = FEFolder
FakePrt4.Name = "Wing "..ssdlllll + 4
local FPWeld4 =  FakePrt4.Weld
FPWeld4.Part0 = FakePrt4
FPWeld4.Part1 = UselessPrt

local FakePrt5 = FakePrt:Clone()
FakePrt5.Parent = FEFolder
FakePrt5.Name = "Wing "..ssdlllll + 5
local FPWeld5 =  FakePrt5.Weld
FPWeld5.Part0 = FakePrt5
FPWeld5.Part1 = UselessPrt

for _, lol in pairs({FakePrt,FakePrt2,FakePrt3,FakePrt4,FakePrt5}) do
lol.Transparency = 0-0+8-8+0+1--9
end

local StarterGui = game:FindFirstChildOfClass("StarterGui")
local BindableEvent = Instancenew("BindableEvent")

local UserInputService = game:FindFirstChildOfClass("UserInputService")
local UserInputType = Enum.UserInputType

local MouseButton1 = UserInputType.MouseButton1
local Touch = UserInputType.Touch

local function Flingy(Target)
    if EnableFling == true then
		if Target and not Target:IsDescendantOf(CharacterClone) and not Target:IsDescendantOf(Character) and not tablefind(Fling, Target) then
			local Parent = Target.Parent

			if Parent:IsA("Model") and Parent ~= Character and Parent:FindFirstChildOfClass("Humanoid") then
				local HumanoidRootPart = FindInstance(Parent, "BasePart", "HumanoidRootPart") or FindInstance(Parent, "BasePart", "Torso") or FindInstance(Parent, "BasePart", "Head")

				if HumanoidRootPart and not tablefind(Fling, HumanoidRootPart) then
					tableinsert(Fling, HumanoidRootPart)
					return
				end
			end

			tableinsert(Fling, Target)
		end
	end
end

local function FlingOnTouch(Partttti)
    if EnableFling == true and FlingOnTouchh == true then
        Partttti.Touched:Connect(function(WhoTouchedMe)
            Flingy(WhoTouchedMe)
        end)
    end
end

local InputBegan = UserInputService.InputBegan:Connect(function(Input, GameProcessed)
	local Type = Input.UserInputType
	if not GameProcessed and ClickToFling == true and ( Type == MouseButton1 or Type == Touch ) then
		Flingy(Mouse.Target) 
	end
end)

local function Part(Name, Size)
	local Part = Instancenew("Part")
	Part.Name = Name
	Part.Size = Size
	Part.Transparency = 1
	Part.Parent = CharacterClone

	return Part
end

local function Motor6D(Name, Part0, Part1, C0, C1)
	local Motor6D = Instancenew("Motor6D")
	Motor6D.Name = Name
	Motor6D.Part0 = Part0
	Motor6D.Part1 = Part1
	Motor6D.C0 = C0
	Motor6D.C1 = C1
	Motor6D.Parent = Part0

	return Motor6D
end

local function Attachment(Name, CFrame, Parent)
	local Attachment = Instancenew("Attachment")
	Attachment.Name = Name
	Attachment.CFrame = CFrame
	Attachment.Parent = Parent

	tableinsert(Attachments, Attachment)
	return Attachment
end

local LimbSize = Vector3new(1, 2, 1)
local TorsoSize = Vector3new(2, 2, 1)

local Head = Part("Head", Vector3new(2, 1, 1))
local Torso = Part("Torso", TorsoSize)
local LeftArm = Part("Left Arm", LimbSize)
local RightArm = Part("Right Arm", LimbSize)
local LeftLeg = Part("Left Leg", LimbSize)
local RightLeg = Part("Right Leg", LimbSize)
local HumanoidRootPart = Part("HumanoidRootPart", TorsoSize)

local Part = nil

if Character then
	Part = FindInstance(Character, "BasePart", "HumanoidRootPart") or FindInstance(Character, "BasePart", "Head") or FindInstance(Character, "BasePart", "Torso") or FindInstance(Character, "BasePart", "UpperTorso")
end

if Part then
	HumanoidRootPart.CFrame = Part.CFrame
else
	local SpawnLocations = { }

	for _, SpawnLocation in pairs(Workspace:GetDescendants()) do
		if SpawnLocation:IsA("SpawnLocation") then
			tableinsert(SpawnLocations, SpawnLocation)
		end
	end

	local Amount = # SpawnLocations

	if Amount > 0 then
		local SpawnLocation = SpawnLocations[mathrandom(1, Amount)]
		HumanoidRootPart.CFrame = SpawnLocation.CFrame * CFramenew(0, SpawnLocation.Size.Y / 2 + 3, 0)
	else
		HumanoidRootPart.CFrame = CFrameidentity
	end
end

local face = Instancenew("Decal")
face.Name = "face"
face.Parent = Head

--Epic's rig
local AccessoryTable = { 
	{ Mesh = "12410145311", Texture = "12404371983", Instance = Head, CFrame = CFramenew(0, 0.75, 0) },
	{ Mesh = "13244366731", Texture = "13244366804", Instance = Torso },
	{ Mesh = "11159370334", Texture = "11159284657", Instance = RightArm, CFrame = CFramenew(0.5, -0.45, 0) },
	{ Mesh = "11263221350", Texture = "11263219250", Instance = LeftArm, CFrame = CFramenew(-0.5, -0.45, 0) },
	{ Mesh = "12652772399", Texture = "12652775021", Instance = RightLeg, CFrame = CFramenew(0, -0.8, 0) },
	{ Mesh = "11159370334", Texture = "11159285454", Instance = LeftLeg, CFrame = CFramenew(0, -0.8, 0) },
	{ Mesh = "4324138105", Texture = "4381699432", Instance = FakePrt, CFrame = CFramenew(0,-4,1.5) * CFrameAngles(1.26,-0.6,1.26) },
	{ Mesh = "3030546036", Texture = "3650205764", Instance = FakePrt2, CFrame = CFramenew(0,-4,1.5) * CFrameAngles(1.26,-0.6,1.26) },
	{ Mesh = "3030546036", Texture = "3860099469", Instance = FakePrt3, CFrame = CFramenew(0,-4,1.5) * CFrameAngles(1.26,-0.6,1.26) },
	{ Mesh = "3030546036", Texture = "4622077774", Instance = FakePrt4, CFrame = CFramenew(0,-4,1.5) * CFrameAngles(1.26,-0.6,1.26) },
	{ Mesh = "4489232754", Texture = "4489233876", Instance = FakePrt5, CFrame = CFramenew(0,-4,1.5) * CFrameAngles(1.26,-0.6,1.26) },
}

for _, Table in pairs(AccessoryTable) do
	if type(Table.Mesh) ~= "string" then
		Table.Mesh = ""
	end
	if type(Table.Texture) ~= "string" then
		Table.Texture = ""
	end
end

Motor6D("Right Shoulder", Torso, RightArm, CFramenew(1, 0.5, 0, 0, 0, 1, 0, 1, 0, -1, -0, -0), CFramenew(-0.5, 0.5, 0, 0, 0, 1, 0, 1, 0, -1, -0, -0))
Motor6D("Left Shoulder", Torso, LeftArm, CFramenew(-1, 0.5, 0, -0, -0, -1, 0, 1, 0, 1, 0, 0), CFramenew(0.5, 0.5, 0, -0, -0, -1, 0, 1, 0, 1, 0, 0))
Motor6D("Right Hip", Torso, RightLeg, CFramenew(1, -1, 0, 0, 0, 1, 0, 1, 0, -1, -0, -0), CFramenew(0.5, 1, 0, 0, 0, 1, 0, 1, 0, -1, -0, -0))
Motor6D("Left Hip", Torso, LeftLeg, CFramenew(-1, -1, 0, -0, -0, -1, 0, 1, 0, 1, 0, 0), CFramenew(-0.5, 1, 0, -0, -0, -1, 0, 1, 0, 1, 0, 0))
Motor6D("Neck", Torso, Head, CFramenew(0, 1, 0, -1, -0, -0, 0, 0, 1, 0, 1, 0), CFramenew(0, -0.5, 0, -1, -0, -0, 0, 0, 1, 0, 1, 0))
Motor6D("RootJoint", HumanoidRootPart, Torso, CFramenew(0, 0, 0, -1, -0, -0, 0, 0, 1, 0, 1, 0), CFramenew(0, 0, 0, -1, -0, -0, 0, 0, 1, 0, 1, 0))

Attachment("HairAttachment", CFramenew(0, 0.600000024, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), Head)
Attachment("HatAttachment", CFramenew(0, 0.600000024, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), Head)
Attachment("FaceFrontAttachment", CFramenew(0, 0, -0.600000024, 1, 0, 0, 0, 1, 0, 0, 0, 1), Head)
Attachment("FaceCenterAttachment", CFramenew(0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), Head)
Attachment("NeckAttachment", CFramenew(0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("BodyFrontAttachment", CFramenew(0, 0, -0.5, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("BodyBackAttachment", CFramenew(0, 0, 0.5, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("LeftCollarAttachment", CFramenew(-1, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("RightCollarAttachment", CFramenew(1, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("WaistFrontAttachment", CFramenew(0, -1, -0.5, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("WaistCenterAttachment", CFramenew(0, -1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("WaistBackAttachment", CFramenew(0, -1, 0.5, 1, 0, 0, 0, 1, 0, 0, 0, 1), Torso)
Attachment("LeftShoulderAttachment", CFramenew(0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), LeftArm)
Attachment("LeftGripAttachment", CFramenew(0, -1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), LeftArm)
Attachment("RightShoulderAttachment", CFramenew(0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), RightArm)
Attachment("RightGripAttachment", CFramenew(0, -1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), RightArm)
Attachment("LeftFootAttachment", CFramenew(0, -1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), LeftLeg)
Attachment("RightFootAttachment", CFramenew(0, -1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), RightLeg)
Attachment("RootAttachment", CFramenew(0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1), HumanoidRootPart)

local Humanoid = Instancenew("Humanoid", CharacterClone)
Instancenew("Animator", Humanoid)

Instancenew("LocalScript", CharacterClone).Name = "Animate"

CharacterClone.PrimaryPart = Head
CharacterClone.Parent = Workspace

local function DescendantAdded(Instance)
	if Instance:IsA("Accessory") then
		taskspawn(function()
			local Handle = WaitForClassOfName(Instance, "BasePart", "Handle")
			local Attachment = WaitForClass(Handle, "Attachment")

			local Clone = Instance:Clone()

			local CloneHandle = FindInstance(Clone, "BasePart", "Handle")
			CloneHandle.Transparency = 1
			CloneHandle:BreakJoints()

			local AccessoryWeld = Instancenew("Weld")
			AccessoryWeld.Name = "AccessoryWeld"
			AccessoryWeld.Part0 = CloneHandle
			AccessoryWeld.C0 = Attachment.CFrame

			local Name = Attachment.Name

			for _, TableAttachment in pairs(Attachments) do
				if TableAttachment.Name == Name then
					AccessoryWeld.Part1 = TableAttachment.Parent
					AccessoryWeld.C1 = TableAttachment.CFrame
				end
			end

			AccessoryWeld.Parent = CloneHandle
			Clone.Parent = CharacterClone

			tableinsert(Accessories, Clone)

			local IsAMeshPart = CloneHandle:IsA("MeshPart")
			local Mesh = IsAMeshPart and CloneHandle or WaitForClass(CloneHandle, "SpecialMesh")
			local Id = IsAMeshPart and "TextureID" or "TextureId"

			for _, Table in pairs(AccessoryTable) do
				local Instance = Table.Instance

				if Instance then
					if stringmatch(Mesh.MeshId, Table.Mesh) and stringmatch(Mesh[Id], Table.Texture) and not tablefind(Blacklist, Instance) then
						tableinsert(Blacklist, Instance)
						tableinsert(Aligns, { Handle, Instance, Table.CFrame or CFrameidentity })
						return
					end
				end
			end

			tableinsert(Aligns, { Handle, CloneHandle, CFrameidentity })
		end)
	elseif Instance:IsA("JointInstance") then
		taskspawn(function()
			taskwait()
			Instance:Destroy()
		end)
	end
end

local function NoCollis()
    for _, b in pairs(Character:GetDescendants()) do
		if b:IsA("BasePart") or b:IsA("Part") then
			b.CanCollide = false
		end
	end
end

coroutine.resume(coroutine.create(function()
    while CharacterClone and task.wait() do
        sinvalue = sinvalue + 1
        NoCollis()
    end
end))

local function CharacterAdded(Character)
	if Character ~= CharacterClone then		
		PostSimulation:Wait()

		local Backpack = LocalPlayer:FindFirstChildOfClass("Backpack")

		if Backpack then
			Backpack:ClearAllChildren()
		end

		tableclear(Aligns)
		tableclear(Blacklist)

		for _, Accessory in pairs(Accessories) do
			Accessory:Destroy()
		end

		local CurrentCameraCFrame = CurrentCamera.CFrame

		LocalPlayer.Character = CharacterClone
		CurrentCamera.CameraSubject = Humanoid

		taskspawn(function()
			CurrentCamera:GetPropertyChangedSignal("CFrame"):Wait()
			CurrentCamera.CFrame = CurrentCameraCFrame
		end)

		local CharacterHumanoidRootPart = WaitForClassOfName(Character, "BasePart", "HumanoidRootPart")

		for Index, Value in pairs(Fling) do
			local BasePart = nil

			if typeof(Value) == "Instance" then 
				if Value:IsA("BasePart") then
					BasePart = Value
				elseif Value:IsA("Humanoid") then
					local Model = Value.Parent

					if Model ~= Character and Model:IsA("Model") then
						BasePart = FindInstance(Model, "BasePart", "HumanoidRootPart") or FindInstance(Model, "BasePart", "Head") or Model:FindFirstChildOfClass("BasePart")
					end
				elseif Value:IsA("Model") and Value ~= Character then
					BasePart = FindInstance(Value, "BasePart", "HumanoidRootPart") or FindInstance(Value, "BasePart", "Head") or Value:FindFirstChildOfClass("BasePart")
				end
			end

			if BasePart then
				local clock = osclock()

				while CharacterHumanoidRootPart and BasePart and osclock() - clock <= 1 and BasePart.AssemblyLinearVelocity.Magnitude <= 60 do
				    NoCollis()
					CharacterHumanoidRootPart.AssemblyAngularVelocity = Velocity
					CharacterHumanoidRootPart.AssemblyLinearVelocity = Velocity
					
					local BasePartVel = (BasePart.Velocity * Vector3new(1, 0, 1)).magnitude
	                local BasePartVerticalVelocity = BasePart.Velocity.y
					
					if BasePart and BasePartVel > 1 then
						CharacterHumanoidRootPart.CFrame = BasePart.CFrame*CFramenew(0,-1,-6.25+-2.75*mathsin(sinvalue/3)*5)
					elseif BasePart and BasePartVel < 1 then
						CharacterHumanoidRootPart.CFrame = BasePart.CFrame
					elseif BasePart and BasePartVerticalVelocity > 1 or BasePartVerticalVelocity < -1 then
					    CharacterHumanoidRootPart.CFrame = CharacterHumanoidRootPart.CFrame * CFramenew(0,0+4*mathsin(sinvalue/3)*5,0)
					end

					PostSimulation:Wait()
				end
			end
		end

		tableclear(Fling)

		if CharacterHumanoidRootPart then
			CharacterHumanoidRootPart.AssemblyAngularVelocity = Vector3zero
			CharacterHumanoidRootPart.AssemblyLinearVelocity = Vector3zero

			CharacterHumanoidRootPart.CFrame = CFramenew(HumanoidRootPart.Position + Vector3new(mathrandom(- 32, 32), 0, mathrandom(- 32, 32)))
			PostSimulation:Wait()
		end

		Character:BreakJoints()

		for _, Instance in pairs(Character:GetDescendants()) do
			DescendantAdded(Instance)
		end

		Character.DescendantAdded:Connect(DescendantAdded)
	end
end

local function Align(Part0, Part1, CFrame)
	if Part0.ReceiveAge == 0 and not Part0.Anchored and # Part0:GetJoints() == 0 then
		Part0.AssemblyAngularVelocity = Vector3new(0, Angular, 0)

		local Part1CFrame = Part1.CFrame
		local LinearVelocity = Part1.AssemblyLinearVelocity * Linear
		local Magnitude = LinearVelocity.Magnitude < Linear

		if Magnitude then
			local LookVector = Part1CFrame.LookVector * Linear
			Part0.AssemblyLinearVelocity = Vector3new(LookVector.X, Linear, LookVector.Z)
		else
			Part0.AssemblyLinearVelocity = Vector3new(LinearVelocity.X, Linear, LinearVelocity.Z)
		end

		Part0.CFrame = Part1CFrame * ( Magnitude and Sleep or CFrameidentity ) * CFrame
	end
end

if Character then
	CharacterAdded(Character)
end

local Added = LocalPlayer.CharacterAdded:Connect(CharacterAdded)

local Connection = game:FindFirstChildOfClass("RunService").PostSimulation:Connect(function()
	local osclock = osclock()
	local Axis = 0.004 * mathcos(osclock * 17.5)

	Sleep = CFramenew(0, Axis, 0)
	Angular = mathcos(osclock)
	Linear = 26

	for _, Table in pairs(Aligns) do
		Align(Table[1], Table[2], Table[3])
	end

	if sethiddenproperty then
		sethiddenproperty(LocalPlayer, "SimulationRadius", 10000000)
	end

	StarterGui:SetCore("ResetButtonCallback", BindableEvent) -- This is if it gets overriden, just like in MyWorld testing place.
end)

local fenv = getfenv()
local shp = fenv.sethiddenproperty or fenv.set_hidden_property or fenv.sethiddenprop
game:GetService("RunService").Heartbeat:Connect(function()
shp(LocalPlayer, "SimulationRadius", 1000)
end)

local function Event()
	CharacterClone:Destroy()
end

BindableEvent.Event:Connect(Event)

CharacterClone:GetPropertyChangedSignal("Parent"):Connect(function()
	if not CharacterClone.Parent then
		Added:Disconnect()
		Connection:Disconnect()

		CharacterClone:Destroy()

		if BindableEvent then
			BindableEvent:Destroy()
		end

		StarterGui:SetCore("ResetButtonCallback", true)
	end
end)

BindableEvent:GetPropertyChangedSignal("Parent"):Connect(Event)

local char = game.Players.LocalPlayer.Character
local Module = game:GetObjects("rbxassetid://4666071449")[1]
local script = Module:FindFirstChildOfClass("Script")
script.Parent = char

local Player = game:GetService("Players").LocalPlayer
local mouse = Player:GetMouse()

warn([[SpectrumGlitcher v7 Loaded.
The super switcher.

Created, Reworked by NoobyGames12
1st Edit by GodzPlaysRB (SpectrumEeveez)
2nd Edit by wanTH092 (Filano)
3rd Edit by AlmightyToast
4th Edit by DerpzDeNugget_YT
5th Edit by KillerzTH
6th Fix by ZaBlazy_y
]])

print([[ CHANGELOG v7.6
The unfinished final update
..
welp updated by filano i think

But FULLLLY fixed by ZaBlazy_y "Momiji" 
(learn to fix)


OY
OY
OYOYOYOYOYOYOYOYOYOYOY
BLUEBLOXGUY35 HERE WITH A MINOR EDIT, SPECTRUM SUCKS, AND  IM HELPING POISON LEAK

also unfair and spectrum dev team are skids



For alpha press B < For Cata press B < P for diversial
]])


--- its obs smooth af do not touch 
---- Sources and functions might be taken from others

-- Nebula stuff --
coroutine.resume(coroutine.create(function()
local S = setmetatable({},{__index = function(s,i) return game:service(i) end})
local CF = {N=CFrame.new,A=CFrame.Angles,fEA=CFrame.fromEulerAnglesXYZ}
local V3 = {N=Vector3.new,FNI=Vector3.FromNormalId,A=Vector3.FromAxis}
local M = {C=math.cos,R=math.rad,S=math.sin,P=math.pi,RNG=math.random,MRS=math.randomseed,H=math.huge,RRNG = function(min,max,div) return math.rad(math.random(min,max)/(div or 1)) end}
local R3 = {N=Region3.new}
local De = S.Debris
local WS = workspace
local Lght = S.Lighting
local RepS = S.ReplicatedStorage
local IN = Instance.new
local Plrs = S.Players
local FXFolder = script:FindFirstChild'Effects'
FXFolder.Parent = nil
local Alpha = .3
	
NewInstance = function(instance,parent,properties)
	local inst = Instance.new(instance)
	inst.Parent = parent
	if(properties)then
		for i,v in next, properties do
			pcall(function() inst[i] = v end)
		end
	end
	return inst;
end
-- Script --

plr = Player
Player = plr 
char = plr.Character
hum = char.Humanoid
hum.HipHeight = 1.3
local cam = game.Workspace.CurrentCamera
Camera = cam
local CamInterrupt = false
local TwoD = false
local TargetInfo = {nil, nil}
cam.CameraType = "Custom"
t = char.Torso
h = char.Head
ra = char["Right Arm"]
la = char["Left Arm"]
rl = char["Right Leg"]
ll = char["Left Leg"]
tors = char.Torso
lleg = char["Left Leg"]
root = char.HumanoidRootPart
hed = char.Head
rleg = char["Right Leg"]
rarm = char["Right Arm"]
larm = char["Left Arm"]
radian = math.rad
random = math.random
Vec3 = Vector3.new
Inst = Instance.new
cFrame = CFrame.new
Euler = CFrame.fromEulerAnglesXYZ
vt = Vector3.new
bc = BrickColor.new
br = BrickColor.random
it = Instance.new
cf = CFrame.new
local Player_Size = 0
cam = game.Workspace.CurrentCamera
CF = CFrame.new
angles = CFrame.Angles
attack = false
Euler = CFrame.fromEulerAnglesXYZ
Rad = math.rad
IT = Instance.new
BrickC = BrickColor.new
Cos = math.cos
Acos = math.acos
Sin = math.sin
Asin = math.asin
Abs = math.abs
Mrandom = math.random
Floor = math.floor
IT = Instance.new
CF = CFrame.new
VT = Vector3.new
RAD = math.rad
C3 = Color3.new
UD2 = UDim2.new
BRICKC = BrickColor.new
ANGLES = CFrame.Angles
EULER = CFrame.fromEulerAnglesXYZ
COS = math.cos
ACOS = math.acos
SIN = math.sin
ASIN = math.asin
ABS = math.abs
MRANDOM = math.random
FLOOR = math.floor
local SINE = 0
local RootCF = CFrame.fromEulerAnglesXYZ(-1.57, 0, 3.14)
local RHCF = CFrame.fromEulerAnglesXYZ(0, 1.6, 0)
local LHCF = CFrame.fromEulerAnglesXYZ(0, -1.6, 0)
local ModeOfGlitch = 1
CF = CFrame.new
local hed = char.Head
local root = char.HumanoidRootPart
local rootj = root.RootJoint
local tors = char.Torso
Player_Size = 1
Cos = math.cos
Sin = math.sin
Rad = math.rad	
CF = CFrame.new
local Diversial = false


local Booleans = {
  CamFollow = true,
  GyroUse = true
}

function lerp(object, newCFrame, alpha)
  return object:lerp(newCFrame, alpha)
end

local Directer = Inst("BodyGyro", root)
Directer.MaxTorque = Vec3(0, 0, 0)
Directer.P = 600000
local CPart = Inst("Part")
CPart.Anchored = true
CPart.CanCollide = false
CPart.Locked = true
CPart.Transparency = 1

local CRAZED = false
local rainbowmode = false
local chaosmode = false
local Error = false
local unstablemode = false
local MAINRUINCOLOR = BrickColor.new("Really red")


local kan = Instance.new("Sound",char)
kan.Volume = 1.25
kan.TimePosition = 0
kan.PlaybackSpeed = 1
kan.Pitch = 1
kan.SoundId = "rbxassetid://415898123"
kan.Name = "wrecked"
kan.Looped = true
kan:Play()

local currentThemePlaying = kan.SoundId
local currentPitch = kan.Pitch
local currentVol = kan.Volume
function newTheme(ID,timepos,pitch,vol)
local kanz = kan
--kanz:Stop()
kanz.Volume = vol
--kanz.TimePosition = timepos
kanz.PlaybackSpeed = pitch
kanz.Pitch = pitch
kanz.SoundId = ID
kanz.Name = "wrecked"
kanz.Looped = true
currentThemePlaying = kanz.SoundId
currentVol = kanz.Volume
currentPitch = kanz.Pitch
--kanz:Play()
--coroutine.resume(coroutine.create(function()
--wait(0.05)
--end))
end


function newThemeCust(ID,timepos,pitch,vol)
local kanz = kan
kanz:Stop()
kanz.Volume = vol
kanz.TimePosition = timepos
kanz.PlaybackSpeed = pitch
kanz.Pitch = pitch
kanz.SoundId = ID
kanz.Name = "wrecked"
kanz.Looped = true
currentThemePlaying = kanz.SoundId
currentVol = kanz.Volume
currentPitch = kanz.Pitch
kanz:Play()
coroutine.resume(coroutine.create(function()
wait(0.05)
end))
end

local mutedtog = false



local Instance = setmetatable({ClearChildrenOfClass = function(where,class,recursive) local children = (recursive and where:GetDescendants() or where:GetChildren()) for _,v in next, children do if(v:IsA(class))then v:destroy();end;end;end},{__index = Instance})

--// Require stuff \\--
function CamShake(who,times,intense,origin) 
end

function CamShakeAll(times,intense,origin)
end

function shakes(power,length)
end



local toggleTag = true
local bilguit = Instance.new("BillboardGui", hed)
bilguit.Adornee = nil
bilguit.Name = "ModeName"
bilguit.Size = UDim2.new(4, 0, 1.2, 0)
bilguit.StudsOffset = Vector3.new(-8, 8/1.5, 0)
local modet = Instance.new("TextLabel", bilguit)
modet.Size = UDim2.new(10/2, 0, 7/2, 0)
modet.FontSize = "Size8"
modet.TextScaled = true
modet.TextTransparency = 0
modet.BackgroundTransparency = 1 
modet.TextTransparency = 0
modet.TextStrokeTransparency = 0
modet.Font = "Antique"
modet.TextStrokeColor3 = Color3.new(1,0,0)
modet.TextColor3 = Color3.new(0.25,0,0)
modet.Text = "MAYHEM"


function chatfunc(text,color,typet,font,timeex)
local chat = coroutine.wrap(function()
if Character:FindFirstChild("TalkingBillBoard")~= nil then
Character:FindFirstChild("TalkingBillBoard"):destroy()
end
local naeeym2 = Instance.new("BillboardGui",Character)
naeeym2.Size = UDim2.new(0,100,0,40)
naeeym2.StudsOffset = Vector3.new(0,3,0)
naeeym2.Adornee = Character.Head
naeeym2.Name = "TalkingBillBoard"
local tecks2 = Instance.new("TextLabel",naeeym2)
tecks2.BackgroundTransparency = 1
tecks2.BorderSizePixel = 0
tecks2.Text = ""
tecks2.Font = font
tecks2.TextSize = 30
tecks2.TextStrokeTransparency = 0
tecks2.TextColor3 = color
tecks2.TextStrokeColor3 = Color3.new(0,0,0)
tecks2.Size = UDim2.new(1,0,0.5,0)
local tecks3 = Instance.new("TextLabel",naeeym2)
tecks3.BackgroundTransparency = 1
tecks3.BorderSizePixel = 0
tecks3.Text = ""
tecks3.Font = font
tecks3.TextSize = 30
tecks3.TextStrokeTransparency = 0
if typet == "Inverted" then
tecks3.TextColor3 = Color3.new(0,0,0)
tecks3.TextStrokeColor3 = color
elseif typet == "Normal" then
tecks3.TextColor3 = color
tecks3.TextStrokeColor3 = Color3.new(0,0,0)
end
tecks3.Size = UDim2.new(1,0,0.5,0)
coroutine.resume(coroutine.create(function()
while true do
swait(1)
if Error == true then
tecks2.TextColor3 = BrickColor.random().Color
tecks3.TextStrokeColor3 = BrickColor.random().Color
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if chaosmode == true then
tecks2.Rotation = tecks2.Rotation + 8 * math.cos(SINE / 16)
tecks3.Rotation = tecks3.Rotation + 8 * math.cos(SINE / 16)
tecks2.TextColor3 = BrickColor.new("White").Color
tecks2.TextColor3 = Color3.new(math.random(0.5,1),0,0)
tecks3.TextStrokeColor3 = Color3.new(math.random(0.5,1),0,0)
tecks3.TextStrokeColor3 = BrickColor.new("White").Color
if math.random(1,10) == 1 then
tecks2.TextColor3 = Color3.new(math.random(0.5,1),0,0)
tecks2.TextColor3 = BrickColor.new("White").Color
tecks3.TextStrokeColor3 = BrickColor.new("White").Color
tecks3.TextStrokeColor3 = Color3.new(math.random(0.5,1),0,0)
end
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait(1)
if chaosmode == true then
tecks2.TextColor3 = BrickColor.random().Color
tecks3.TextStrokeColor3 = BrickColor.random().Color
end
end
end))
modet.TextTransparency = modet.TextTransparency  + 1
modet.TextStrokeTransparency = modet.TextStrokeTransparency + 1
for i = 0, 74*timeex do
swait()
modet.TextTransparency = 1
modet.TextStrokeTransparency = 1
tecks2.Text = text
tecks3.Text = text
end
local randomrot = math.random(1,2)
if randomrot == 1 then
for i = 1, 50 do
swait()
tecks2.Text = text
tecks3.Text = text
modet.TextTransparency = modet.TextTransparency - .02
modet.TextStrokeTransparency = modet.TextStrokeTransparency - .02
tecks2.TextStrokeTransparency = tecks2.TextStrokeTransparency +.04
tecks2.TextTransparency = tecks2.TextTransparency + .04
tecks3.TextStrokeTransparency = tecks2.TextStrokeTransparency +.04
tecks3.TextTransparency = tecks2.TextTransparency + .04
end
elseif randomrot == 2 then
	for i = 1, 50 do
swait()
tecks2.Text = text
tecks3.Text = text
modet.TextTransparency = modet.TextTransparency - .02
modet.TextStrokeTransparency = modet.TextStrokeTransparency - .02
tecks2.TextStrokeTransparency = tecks2.TextStrokeTransparency +.04
tecks2.TextTransparency = tecks2.TextTransparency + .04
tecks3.TextStrokeTransparency = tecks2.TextStrokeTransparency +.04
tecks3.TextTransparency = tecks2.TextTransparency + .04
end
end
modet.TextTransparency = 0
modet.TextStrokeTransparency = 0
if toggleTag == false then
modet.TextTransparency = 1
modet.TextStrokeTransparency = 1
end
naeeym2:Destroy()
end)
chat()
end

local rai = {"USER","User","USer","USEr","uSER","usER","useR","uSer","usEr","useR","PlAyEr","666"}

function bosschatfunc(text,color,watval)
for i,v in pairs(game:GetService("Players"):GetPlayers()) do
coroutine.resume(coroutine.create(function()
if v.PlayerGui:FindFirstChild("Dialog")~= nil then
v.PlayerGui:FindFirstChild("Dialog"):destroy()
end
local scrg = Instance.new("ScreenGui",v.PlayerGui)
scrg.ResetOnSpawn = false
CFuncs["EchoSound"].Create("rbxassetid://525200869", scrg, 0.5, 1,0,10,0.1,0.25,1)
scrg.Name = "Dialog"
local txtlb = Instance.new("TextLabel",scrg)
txtlb.Text = ""
txtlb.Font = "Arcade"
txtlb.TextColor3 = Color3.new(0,0,0)
txtlb.TextStrokeTransparency = 0
txtlb.BackgroundTransparency = 0.75
txtlb.BackgroundColor3 = Color3.new(0,0,0)
txtlb.TextStrokeColor3 = color
txtlb.TextScaled = true
txtlb.Size = UDim2.new(1,0,0.25,0)
txtlb.TextXAlignment = "Left"
txtlb.Position = UDim2.new(0,0,0.75 + 1,0)
local txtlb2 = Instance.new("TextLabel",scrg)
txtlb2.Text = modet.Text.." USER:"
txtlb2.Font = "Arcade"
txtlb2.TextColor3 = Color3.new(0,0,0)
txtlb2.TextStrokeTransparency = 0
txtlb2.BackgroundTransparency = 1
txtlb2.TextStrokeColor3 = color
txtlb2.TextSize = 40
txtlb2.Size = UDim2.new(1,0,0.25,0)
txtlb2.TextXAlignment = "Left"
txtlb2.Position = UDim2.new(0,0,1,0)
local fvalen = 0.55
local fval = -0.49
coroutine.resume(coroutine.create(function()
while true do
swait()
if ModeOfGlitch == 666666 then
txtlb2.Text = modet.Text.."  U S E R :"
txtlb.Rotation = math.random(-2,2)
txtlb2.Rotation = math.random(-2,2)
txtlb.Position = txtlb.Position + UDim2.new(0,math.random(-2,2)/5,0,math.random(-2,2)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-2,2)/5,0,math.random(-2,2)/5)
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if Error == true then
txtlb2.Text = modet.Text..rai[math.random(1,12)]
txtlb.Rotation = math.random(-9,9)
txtlb2.Rotation = math.random(-9,9)
txtlb.Position = txtlb.Position + UDim2.new(0,math.random(-8,8)/5,0,math.random(-8,8)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-8,8)/5,0,math.random(-8,8)/5)
txtlb.TextStrokeColor3 = BrickColor.random().Color
txtlb2.TextStrokeColor3 = BrickColor.random().Color
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if unstablemode == true then
txtlb.TextStrokeColor3 = BrickColor.new(math.random(0,1),0,0).Color
txtlb2.TextStrokeColor3 = BrickColor.new(math.random(0,1),0,0).Color
txtlb.TextColor3 = BrickColor.new(1,1,1).Color
txtlb2.TextColor3 = BrickColor.new(1,1,1).Color
if math.random(1,10) == 1 then
txtlb.TextStrokeColor3 = BrickColor.new(0,0,0).Color
txtlb2.TextStrokeColor3 = BrickColor.new(0,0,0).Color
txtlb.TextColor3 = BrickColor.new(math.random(0,1),0,0).Color
txtlb2.TextColor3 = BrickColor.new(math.random(0,1),0,0).Color

end
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if CRAZED == true then
txtlb.Rotation = math.random(-4,4)
txtlb2.Rotation = math.random(-4,4)
txtlb.Position = txtlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb.TextStrokeColor3 = BrickColor.new(0,0,math.random(0,1)).Color
txtlb.TextColor3 = BrickColor.new(0,0,math.random(0,1)).Color
txtlb2.TextStrokeColor3 = BrickColor.new(0,0,math.random(0,1)).Color
txtlb2.TextColor3 = BrickColor.new(0,0,math.random(0,1)).Color
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if chaosmode == true then
txtlb.Rotation = math.random(-1,1)
txtlb2.Rotation = math.random(-1,1)
txtlb.Position = txtlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb.TextStrokeColor3 = BrickColor.random().Color
txtlb2.TextStrokeColor3 = BrickColor.random().Color
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if scrg.Parent ~= nil then
	fvalen = fvalen - 0.0001
elseif scrg.Parent == nil then
break
end
end
end))
local flol = 1.75
local flil = 1.6
coroutine.resume(coroutine.create(function()
	for i = 0, 9 do
		swait()
		fval = fval + 0.05
		flol = flol - 0.1
		flil = flil - 0.1
		txtlb.Text = ""
		txtlb.Position = UDim2.new(0,0,flol,0)
		txtlb2.Position = UDim2.new(0,0,flil,0)
	end
	txtlb.Text = text
wait(watval)
local valinc = 0
for i = 0, 99 do
swait()
valinc = valinc + 0.0001
flol = flol + valinc
flil = flil + valinc
txtlb.Rotation = txtlb.Rotation + valinc*20
txtlb2.Rotation = txtlb2.Rotation - valinc*50
txtlb.Position = UDim2.new(0,0,flol,0)
txtlb2.Position = UDim2.new(0,0,flil,0)
txtlb.TextStrokeTransparency = txtlb.TextStrokeTransparency + 0.01
txtlb.TextTransparency = txtlb.TextTransparency + 0.01
txtlb2.TextStrokeTransparency = txtlb2.TextStrokeTransparency + 0.01
txtlb2.TextTransparency = txtlb2.TextTransparency + 0.01
txtlb.BackgroundTransparency = txtlb.BackgroundTransparency + 0.0025
end
scrg:Destroy()
end))
end))
end
end

FELOADLIBRARY = {}
loadstring(game:GetObjects("rbxassetid://5209815302")[1].Source)()
local Create = FELOADLIBRARY.Create

CFuncs = {	
	["Part"] = {
		Create = function(Parent, Material, Reflectance, Transparency, BColor, Name, Size)
			local Part = Create("Part"){
				Parent = Parent,
				Reflectance = Reflectance,
				Transparency = Transparency,
				CanCollide = false,
				Locked = true,
				BrickColor = BrickColor.new(tostring(BColor)),
				Name = Name,
				Size = Size,
				Material = Material,
			}
			RemoveOutlines(Part)
			return Part
		end;
	};
	
	["Mesh"] = {
		Create = function(Mesh, Part, MeshType, MeshId, OffSet, Scale)
			local Msh = Create(Mesh){
				Parent = Part,
				Offset = OffSet,
				Scale = Scale,
			}
			if Mesh == "SpecialMesh" then
				Msh.MeshType = MeshType
				Msh.MeshId = MeshId
			end
			return Msh
		end;
	};
	
	["Mesh"] = {
		Create = function(Mesh, Part, MeshType, MeshId, OffSet, Scale)
			local Msh = Create(Mesh){
				Parent = Part,
				Offset = OffSet,
				Scale = Scale,
			}
			if Mesh == "SpecialMesh" then
				Msh.MeshType = MeshType
				Msh.MeshId = MeshId
			end
			return Msh
		end;
	};
	
	["Weld"] = {
		Create = function(Parent, Part0, Part1, C0, C1)
			local Weld = Create("Weld"){
				Parent = Parent,
				Part0 = Part0,
				Part1 = Part1,
				C0 = C0,
				C1 = C1,
			}
			return Weld
		end;
	};

	["Sound"] = {
		Create = function(id, par, vol, pit) 
			coroutine.resume(coroutine.create(function()
				local S = Create("Sound"){
					Volume = vol,
                                        Name = "EffectSoundo",
					Pitch = pit or 1,
					SoundId = id,
					Parent = par or workspace,
				}
				wait() 
				S:play() 
				game:GetService("Debris"):AddItem(S, 10)
			end))
		end;
	};

	["TimeSound"] = {
		Create = function(id, par, vol, pit, timepos) 
			coroutine.resume(coroutine.create(function()
				local S = Create("Sound"){
					Volume = vol,
                                        Name = "EffectSoundo",
					Pitch = pit or 1,
					SoundId = id,
                                        TimePosition = timepos,
					Parent = par or workspace,
				}
				wait() 
				S:play() 
				game:GetService("Debris"):AddItem(S, 10)
			end))
		end;
	};
		["EchoSound"] = {
		Create = function(id, par, vol, pit, timepos,delays,echodelay,fedb,dryl) 
			coroutine.resume(coroutine.create(function()
				local Sas = Create("Sound"){
					Volume = vol,
                    Name = "EffectSoundo",
					Pitch = pit or 1,
					SoundId = id,
                    TimePosition = timepos,
					Parent = par or workspace,
				}
				local E = Create("EchoSoundEffect"){
					Delay = echodelay,
                    Name = "Echo",
					Feedback = fedb,
                    DryLevel = dryl,
					Parent = Sas,
				}
				wait() 
				Sas:play() 
				game:GetService("Debris"):AddItem(Sas, delays)
			end))
		end;
	};

["LongSound"] = {
		Create = function(id, par, vol, pit) 
			coroutine.resume(coroutine.create(function()
				local S = Create("Sound"){
					Volume = vol,
					Pitch = pit or 1,
					SoundId = id,
					Parent = par or workspace,
				}
				wait() 
				S:play() 
				game:GetService("Debris"):AddItem(S, 60)
			end))
		end;
	};
	
	["ParticleEmitter"] = {
		Create = function(Parent, Color1, Color2, LightEmission, Size, Texture, Transparency, ZOffset, Accel, Drag, LockedToPart, VelocityInheritance, EmissionDirection, Enabled, LifeTime, Rate, Rotation, RotSpeed, Speed, VelocitySpread)
			local fp = Create("ParticleEmitter"){
				Parent = Parent,
				Color = ColorSequence.new(Color1, Color2),
				LightEmission = LightEmission,
				Size = Size,
				Texture = Texture,
				Transparency = Transparency,
				ZOffset = ZOffset,
				Acceleration = Accel,
				Drag = Drag,
				LockedToPart = LockedToPart,
				VelocityInheritance = VelocityInheritance,
				EmissionDirection = EmissionDirection,
				Enabled = Enabled,
				Lifetime = LifeTime,
				Rate = Rate,
				Rotation = Rotation,
				RotSpeed = RotSpeed,
				Speed = Speed,
				VelocitySpread = VelocitySpread,
			}
			return fp
		end;
	};

	CreateTemplate = {
	
	};
}



New = function(Object, Parent, Name, Data)
	local Object = Instance.new(Object)
	for Index, Value in pairs(Data or {}) do
		Object[Index] = Value
	end
	Object.Parent = Parent
	Object.Name = Name
	return Object
end
local halocolor = BrickColor.new("Pastel light blue")
local halocolor2 = BrickColor.new("Cool yellow")
local starcolor = BrickColor.new("Bright yellow")
local lunacolor = BrickColor.new("Navy blue")
local lunacolor2 = BrickColor.new("Bright blue")
local wepcolor = BrickColor.new("Really black")
local maincolor = BrickColor.new("Really black")
local m = Instance.new("Model",char)
local m2 = Instance.new("Model",char)
local m3 = Instance.new("Model",char)
local mw1 = Instance.new("Model",char)
local mw2 = Instance.new("Model",char)

local est = {"error","Errors","eRror","erRor","errOr","erroR","ERror","ErRor","ErrOr","ErroR","eRRor","eRrOr","eRroR","erROr","erRoR","errOR","ERROR"}



gui = function(GuiType, parent, text, backtrans, backcol, pos, size)
  local gui = it(GuiType)
  gui.Parent = parent
  gui.Text = text
  gui.BackgroundTransparency = backtrans
  gui.BackgroundColor3 = backcol
  gui.SizeConstraint = "RelativeXY"
  gui.TextXAlignment = "Center"
  gui.TextYAlignment = "Center"
  gui.Position = pos
  gui.Size = size
  gui.Font = "SourceSans"
  gui.FontSize = "Size14"
  gui.TextWrapped = false
  gui.TextStrokeTransparency = 0
  gui.TextColor = BrickColor.new("White")
  return gui
end
--------------------------- GUI STUFF
local basgui = it("GuiMain")
basgui.Parent = plr.PlayerGui
basgui.Name = "VISgui"
basgui.ResetOnSpawn = false
local fullscreenz = it("Frame")
fullscreenz.Parent = basgui
fullscreenz.BackgroundColor3 = Color3.new(255, 255, 255)
fullscreenz.BackgroundTransparency = 1
fullscreenz.BorderColor3 = Color3.new(17, 17, 17)
fullscreenz.Size = UDim2.new(1, 0, 1, 0)
fullscreenz.Position = UDim2.new(0, 0, 0, 0)
local imgl2 = Instance.new("ImageLabel",fullscreenz)
imgl2.BackgroundTransparency = 1
imgl2.BorderSizePixel = 0
imgl2.ImageTransparency = 0.5
imgl2.ImageColor3 = Color3.new(1,0,0)
imgl2.Position = UDim2.new(0.75,-200,0.55,-200)
imgl2.Size = UDim2.new(0,1000,0,1000)
imgl2.Image = "rbxassetid://2076458450"
local techc = Instance.new("ImageLabel",fullscreenz)
techc.BackgroundTransparency = 1
techc.BorderSizePixel = 0
techc.ImageTransparency = 0.5
techc.ImageColor3 = Color3.new(1,0,0)
techc.Position = UDim2.new(0.75,-200,0.55,-200)
techc.Size = UDim2.new(0,1000,0,1000)
techc.Image = "rbxassetid://2092248396"
local circl = imgl2:Clone()
circl.Parent = fullscreenz
circl.ImageTransparency = 0
circl.Size = UDim2.new(0,550,0,550)
circl.Position = UDim2.new(0.75,25,0.55,25)
circl.ImageColor3 = Color3.new(0,0,0)
circl.Image = "rbxassetid://2365416622"
local circl2 = imgl2:Clone()
circl2.Parent = fullscreenz
circl2.ImageTransparency = 0
circl2.Size = UDim2.new(0,700,0,700)
circl2.Position = UDim2.new(0.75,-50,0.55,-50)
circl2.ImageColor3 = Color3.new(1,0,0)
circl2.Image = "rbxassetid://2365431459"
local imgl2b = imgl2:Clone()
imgl2b.Parent = fullscreenz
imgl2b.ImageTransparency = 0
imgl2b.Size = UDim2.new(0,800,0,800)
imgl2b.Position = UDim2.new(0.75,-100,0.55,-100)
imgl2b.ImageColor3 = Color3.new(0,0,0)
local ned = Instance.new("TextLabel",fullscreenz)
ned.ZIndex = 2
ned.Font = "Arcade"
ned.BackgroundTransparency = 1
ned.BorderSizePixel = 0.65
ned.Size = UDim2.new(0.3,0,0.2,0)
ned.Position = UDim2.new(0.7,0,0.8,0)
ned.TextColor3 = BrickColor.new("Really red").Color
ned.TextStrokeColor3 = BrickColor.new("Really black").Color
ned.TextScaled = true
ned.TextStrokeTransparency = 0
ned.Text = "MAYHEM"
ned.TextSize = 24
ned.Rotation = 1
ned.TextXAlignment = "Right"
ned.TextYAlignment = "Bottom"
local wobble = Instance.new("Frame",basgui)
wobble.Name = "Wobble"
wobble.BackgroundTransparency = 0.5
wobble.Size = UDim2.new(1.1,0,1.1,0)
wobble.Position = UDim2.new(-0.08,0,0.943,0)
local wobble2 = Instance.new("Frame",basgui)
wobble2.Name = "wobble2"
wobble2.BackgroundTransparency = 0.5
wobble2.Size = UDim2.new(1.1,0,0.09,0)
wobble2.Position = UDim2.new(-0.08,0,0.878,0)
local Visuals = Instance.new("Frame",basgui)
Visuals.Name = "Visuals"
Visuals.BackgroundTransparency = 0.3
Visuals.Size = UDim2.new(0,100,0,100)
Visuals.Position = UDim2.new(0.462,0,0.826,0)
local glow = Instance.new("ImageLabel",basgui)
glow.Name = "glow"
glow.BackgroundTransparency = 1
glow.ImageTransparency = 0
glow.Image = "rbxassetid://2344870656"
glow.Size = UDim2.new(0,0,0,0)
glow.Position = UDim2.new(0.026,0,-0.235,0)
local Visuals2 = Instance.new("Frame",basgui)
Visuals2.Name = "Visuals2"
Visuals2.BackgroundTransparency = 0.3
Visuals2.Size = UDim2.new(0,50,0,50)
Visuals2.Position = UDim2.new(0.48,0,0.867,0) -- {0.476, 0},{0.901, 0}
local TextFrame = Instance.new("TextLabel",basgui)
TextFrame.Name = "Farmer"
TextFrame.Font = "Arcade"
TextFrame.Text = "Spectrum Glitcher"
TextFrame.TextSize = 60
TextFrame.BackgroundTransparency = 1
TextFrame.Size = UDim2.new(0,42,0,42)
TextFrame.Position = UDim2.new(0.48,0,0.867,0) -- {0.476, 0},{0.867, 0}
-- local TextFrame2 = Instance.new("TextLabel",basgui)
-- TextFrame2.Name = "Farmer3"
-- TextFrame2.Font = "Arcade"
-- TextFrame2.Text = ""
-- TextFrame2.TextXAlignment = "Center"
-- TextFrame2.TextSize = 40
-- TextFrame2.BackgroundTransparency = 1
-- TextFrame2.Size = UDim2.new(0,1367,0,42)
-- TextFrame2.Position = UDim2.new(0,0,0,0) -- {0.476, 0},{0.901, 0}
local DamageFrame = Instance.new("TextLabel",basgui)
DamageFrame.Name = "Farmer2"
DamageFrame.Font = "SciFi"
DamageFrame.Text = "Fling = Click"
DamageFrame.TextSize = 30
DamageFrame.BackgroundTransparency = 1
DamageFrame.TextStrokeTransparency = 0
DamageFrame.Size = UDim2.new(0,42,0,42)
DamageFrame.Rotation = 15
DamageFrame.Position = UDim2.new(0.069,0,0.91,0) -- {0.476, 0},{0.901, 0}
local ModeFrame = Instance.new("TextLabel",basgui)
ModeFrame.Name = "Farmer5000"
ModeFrame.Font = "SciFi"
ModeFrame.Text = "Side: Spectrum"
ModeFrame.TextSize = 30
ModeFrame.BackgroundTransparency = 1
ModeFrame.TextStrokeTransparency = 0
ModeFrame.Size = UDim2.new(0,42,0,42)
ModeFrame.Rotation = 15
ModeFrame.Position = UDim2.new(0.069,0,0.86,0) -- {0.476, 0},{0.901, 0}


local extrawingmod1 = Instance.new("Model",char)
local extrawingmod2 = Instance.new("Model",char)

function CreateParta(parent,transparency,reflectance,material,brickcolor)
local p = Instance.new("Part")
p.TopSurface = 0
p.BottomSurface = 0
p.Parent = parent
p.Size = Vector3.new(0.1,0.1,0.1)
p.Transparency = transparency
p.Reflectance = reflectance
p.CanCollide = false
p.Locked = true
p.BrickColor = brickcolor
p.Material = material
return p
end

function CreateMesh(parent,meshtype,x1,y1,z1)
local mesh = Instance.new("SpecialMesh",parent)
mesh.MeshType = meshtype
mesh.Scale = Vector3.new(x1*10,y1*10,z1*10)
return mesh
end

function CreateSpecialMesh(parent,meshid,x1,y1,z1)
local mesh = Instance.new("SpecialMesh",parent)
mesh.MeshType = "FileMesh"
mesh.MeshId = meshid
mesh.Scale = Vector3.new(x1,y1,z1)
return mesh
end


function CreateSpecialGlowMesh(parent,meshid,x1,y1,z1)
local mesh = Instance.new("SpecialMesh",parent)
mesh.MeshType = "FileMesh"
mesh.MeshId = meshid
mesh.TextureId = "http://www.roblox.com/asset/?id=269748808"
mesh.Scale = Vector3.new(x1,y1,z1)
mesh.VertexColor = Vector3.new(parent.BrickColor.r, parent.BrickColor.g, parent.BrickColor.b)
return mesh
end

function CreateWeld(parent,part0,part1,C1X,C1Y,C1Z,C1Xa,C1Ya,C1Za,C0X,C0Y,C0Z,C0Xa,C0Ya,C0Za)
local weld = Instance.new("Weld")
weld.Parent = parent
weld.Part0 = part0
weld.Part1 = part1
weld.C1 = CFrame.new(C1X,C1Y,C1Z)*CFrame.Angles(C1Xa,C1Ya,C1Za)
weld.C0 = CFrame.new(C0X,C0Y,C0Z)*CFrame.Angles(C0Xa,C0Ya,C0Za)
return weld
end


--------------
-------------- ground effect
local cen = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
CreateWeld(cen,root,cen,0,3,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
local effar = Instance.new("ParticleEmitter",cen)
effar.Texture = "rbxassetid://2344870656"
effar.LightEmission = 1
effar.Color = ColorSequence.new(Color3.new(1,0,0))
effar.Rate = 50
effar.Enabled = false
effar.EmissionDirection = "Front"
effar.Lifetime = NumberRange.new(1)
effar.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,15,0),NumberSequenceKeypoint.new(0.1,5,0),NumberSequenceKeypoint.new(0.8,15,0),NumberSequenceKeypoint.new(1,40,0)})
effar.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.8,0.5,0),NumberSequenceKeypoint.new(1,1,0)})
effar.Speed = NumberRange.new(80,90)
effar.Acceleration = Vector3.new(0,10,0)
effar.Drag = 5
effar.Rotation = NumberRange.new(-500,500)
effar.SpreadAngle = Vector2.new(0,900)
effar.RotSpeed = NumberRange.new(-500,500)

----
local sorb = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
CreateWeld(sorb,rarm,sorb,0,1,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
local sorb2 = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
CreateWeld(sorb2,larm,sorb2,0,1,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

local handlex = CreateParta(mw2,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0,0,0)
local handlexweld = CreateWeld(handlex,tors,handlex,0,-1.5,-1.05,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
local valuaring = 10
for i = 0, 49 do
	valuaring = valuaring + 10
rn = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(rn,"Brick",0.25,0.1,0.1)
CreateWeld(rn,handlex,rn,0,1,0,math.rad(0),math.rad(0),math.rad(valuaring),0,0,0,math.rad(0),math.rad(0),math.rad(0))
end

local refec = Instance.new("ParticleEmitter",handlex)
refec.Texture = "rbxassetid://249270319"
refec.LightEmission = 0.95
refec.Color = ColorSequence.new(BrickColor.new("Really red").Color)
refec.Rate = 50
refec.Lifetime = NumberRange.new(0.5)
refec.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0.5,0),NumberSequenceKeypoint.new(0.5,0.75,0),NumberSequenceKeypoint.new(1,0.1,0)})
refec.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.5,0.25,0),NumberSequenceKeypoint.new(1,1,0)})
refec.Speed = NumberRange.new(0,2)
refec.Drag = 5
refec.LockedToPart = true
refec.Rotation = NumberRange.new(-500,500)
refec.VelocitySpread = 9000
refec.RotSpeed = NumberRange.new(-500,500)

local handle = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local handleweld = CreateWeld(handle,tors,handle,0,-1.5,-1.05,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

--"rbxassetid://31727915"
--- Left wing.

local lwing1 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local lwing1weld = CreateWeld(lwing1,handle,lwing1,3,0,0,math.rad(5),math.rad(0),math.rad(12.5),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,lwing1,wed,0,0,0.25,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,lwing1,wed,0,0,0.25,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25,0.25)
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,3)
CreateWeld(wed,lwing1,wed,0,-0.25,1.75,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,-0.25,-2)
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,3,0.5)
CreateWeld(wed,lwing1,wed,0,-1.75,0.25,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))

tl1 = Instance.new('Trail',wed)
tl1.Attachment0 = A1
tl1.Attachment1 = A0
tl1.Texture = "rbxassetid://2108945559"
tl1.LightEmission = 1
tl1.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tl1.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tl1.Lifetime = 0.6


local lwing2 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local lwing2weld = CreateWeld(lwing2,handle,lwing2,4,1,0,math.rad(10),math.rad(0),math.rad(25),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,lwing2,wed,0,0,0.25,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,lwing2,wed,0,0,0.25,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25,0.25)
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,3)
CreateWeld(wed,lwing2,wed,0,-0.25,1.75,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,-0.25,-2)
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,3,0.5)
CreateWeld(wed,lwing2,wed,0,-1.75,0.25,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))

tl2 = Instance.new('Trail',wed)
tl2.Attachment0 = A1
tl2.Attachment1 = A0
tl2.Texture = "rbxassetid://2108945559"
tl2.LightEmission = 1
tl2.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tl2.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tl2.Lifetime = 0.6

local lwing3 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local lwing3weld = CreateWeld(lwing3,handle,lwing3,4.75,2,0,math.rad(15),math.rad(0),math.rad(37.5),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,lwing3,wed,0,0,0.25,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,lwing3,wed,0,0,0.25,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25,0.25)
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,3)
CreateWeld(wed,lwing3,wed,0,-0.25,1.75,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,-0.25,-2)
wed = CreateParta(mw1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,3,0.5)
CreateWeld(wed,lwing3,wed,0,-1.75,0.25,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))

tl3 = Instance.new('Trail',wed)
tl3.Attachment0 = A1
tl3.Attachment1 = A0
tl3.Texture = "rbxassetid://2108945559"
tl3.LightEmission = 1
tl3.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tl3.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tl3.Lifetime = 0.6
local lwing4 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local lwing4weld = CreateWeld(lwing4,handle,lwing4,5.75,3,0,math.rad(20),math.rad(0),math.rad(50),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,lwing4,wed,0,0,0.25*2,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,lwing4,wed,0,0,0.25*2,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25*2,0.25*2)
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,3*2)
CreateWeld(wed,lwing4,wed,0,-0.25*2,1.75*2,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,-0.25*2,-2*2)
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.0*25,3*2,0.5*2)
CreateWeld(wed,lwing4,wed,0,-1.75*2,0.25*2,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))

tl4 = Instance.new('Trail',wed)
tl4.Attachment0 = A1
tl4.Attachment1 = A0
tl4.Texture = "rbxassetid://2108945559"
tl4.LightEmission = 1
tl4.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tl4.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tl4.Lifetime = 0.6
local lwing5 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local lwing5weld = CreateWeld(lwing5,handle,lwing5,6.75,4,0,math.rad(25),math.rad(0),math.rad(62.5),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,lwing5,wed,0,0,0.25*2,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,lwing5,wed,0,0,0.25*2,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25*2,0.25*2)
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,3*2)
CreateWeld(wed,lwing5,wed,0,-0.25*2,1.75*2,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,-0.25*2,-2*2)
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,3*2,0.5*2)
CreateWeld(wed,lwing5,wed,0,-1.75*2,0.25*2,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))

tl5 = Instance.new('Trail',wed)
tl5.Attachment0 = A1
tl5.Attachment1 = A0
tl5.Texture = "rbxassetid://2108945559"
tl5.LightEmission = 1
tl5.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tl5.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tl5.Lifetime = 0.6
local lwing6 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local lwing6weld = CreateWeld(lwing6,handle,lwing6,7.75,5,0,math.rad(30),math.rad(0),math.rad(75),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,lwing6,wed,0,0,0.25*2,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,lwing6,wed,0,0,0.25*2,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25*2,0.25*2)
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,3*2)
CreateWeld(wed,lwing6,wed,0,-0.25*2,1.75*2,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,-0.25*2,-2*2)
wed = CreateParta(extrawingmod1,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,3*2,0.5*2)
CreateWeld(wed,lwing6,wed,0,-1.75*2,0.25*2,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
tl6 = Instance.new('Trail',wed)
tl6.Attachment0 = A1
tl6.Attachment1 = A0
tl6.Texture = "rbxassetid://2108945559"
tl6.LightEmission = 1
tl6.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tl6.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tl6.Lifetime = 0.6

tl1.Enabled = false
tl2.Enabled = false
tl3.Enabled = false
tl4.Enabled = false
tl5.Enabled = false
tl6.Enabled = false
-- Right wing.

local rwing1 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local rwing1weld = CreateWeld(rwing1,handle,rwing1,-3,0,0,math.rad(5),math.rad(0),math.rad(-12.5),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,rwing1,wed,0,0,0.25,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25,0.25)
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,rwing1,wed,0,0,0.25,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,3)
CreateWeld(wed,rwing1,wed,0,-0.25,1.75,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,3,0.5)
CreateWeld(wed,rwing1,wed,0,-1.75,0.25,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,2,0.25)

tr1 = Instance.new('Trail',wed)
tr1.Attachment0 = A1
tr1.Attachment1 = A0
tr1.Texture = "rbxassetid://2108945559"
tr1.LightEmission = 1
tr1.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tr1.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tr1.Lifetime = 0.6

local rwing2 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local rwing2weld = CreateWeld(rwing2,handle,rwing2,-4,1,0,math.rad(10),math.rad(0),math.rad(-25),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,rwing2,wed,0,0,0.25,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25,0.25)
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,rwing2,wed,0,0,0.25,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,3)
CreateWeld(wed,rwing2,wed,0,-0.25,1.75,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,3,0.5)
CreateWeld(wed,rwing2,wed,0,-1.75,0.25,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,2,0.25)

tr2 = Instance.new('Trail',wed)
tr2.Attachment0 = A1
tr2.Attachment1 = A0
tr2.Texture = "rbxassetid://2108945559"
tr2.LightEmission = 1
tr2.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tr2.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tr2.Lifetime = 0.6

local rwing3 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local rwing3weld = CreateWeld(rwing3,handle,rwing3,-4.75,2,0,math.rad(15),math.rad(0),math.rad(-37.5),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,rwing3,wed,0,0,0.25,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25,0.25)
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,0.5)
CreateWeld(wed,rwing3,wed,0,0,0.25,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,0.5,3)
CreateWeld(wed,rwing3,wed,0,-0.25,1.75,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(mw2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05,3,0.5)
CreateWeld(wed,rwing3,wed,0,-1.75,0.25,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,2,0.25)

tr3 = Instance.new('Trail',wed)
tr3.Attachment0 = A1
tr3.Attachment1 = A0
tr3.Texture = "rbxassetid://2108945559"
tr3.LightEmission = 1
tr3.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tr3.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tr3.Lifetime = 0.6


local rwing4 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local rwing4weld = CreateWeld(rwing4,handle,rwing4,-5.75,3,0,math.rad(20),math.rad(0),math.rad(-50),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,rwing4,wed,0,0,0.25*2,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25*2,0.25*2)
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,rwing4,wed,0,0,0.25*2,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,3*2)
CreateWeld(wed,rwing4,wed,0,-0.25*2,1.75*2,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,3*2,0.5*2)
CreateWeld(wed,rwing4,wed,0,-1.75*2,0.25*2,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,2,0.25)

tr4 = Instance.new('Trail',wed)
tr4.Attachment0 = A1
tr4.Attachment1 = A0
tr4.Texture = "rbxassetid://2108945559"
tr4.LightEmission = 1
tr4.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tr4.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tr4.Lifetime = 0.6

local rwing5 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local rwing5weld = CreateWeld(rwing5,handle,rwing5,-6.75,4,0,math.rad(25),math.rad(0),math.rad(-62.5),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,rwing5,wed,0,0,0.25*2,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25*2,0.25*2)
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,rwing5,wed,0,0,0.25*2,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,3*2)
CreateWeld(wed,rwing5,wed,0,-0.25*2,1.75*2,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,3*2,0.5*2)
CreateWeld(wed,rwing5,wed,0,-1.75*2,0.25*2,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,2,0.25)

tr5 = Instance.new('Trail',wed)
tr5.Attachment0 = A1
tr5.Attachment1 = A0
tr5.Texture = "rbxassetid://2108945559"
tr5.LightEmission = 1
tr5.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tr5.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tr5.Lifetime = 0.6

local rwing6 = CreateParta(m,1,1,"Neon",maincolor)
CreateMesh(handle,"Brick",0.5,0.5,0.5)
local rwing6weld = CreateWeld(rwing6,handle,rwing6,-7.75,3,0,math.rad(30),math.rad(0),math.rad(-75),0,0,0,math.rad(0),math.rad(0),math.rad(0))

wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,rwing6,wed,0,0,0.25*2,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A0 = Instance.new('Attachment',wed)
A0.Position = vt(0,0.25*2,0.25*2)
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,0.5*2)
CreateWeld(wed,rwing6,wed,0,0,0.25*2,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,0.5*2,3*2)
CreateWeld(wed,rwing6,wed,0,-0.25*2,1.75*2,math.rad(0),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
wed = CreateParta(extrawingmod2,0,0,"Neon",halocolor)
CreateMesh(wed,"Wedge",0.05*2,3*2,0.5*2)
CreateWeld(wed,rwing6,wed,0,-1.75*2,0.25*2,math.rad(90),math.rad(90),math.rad(90),0,0,0,math.rad(0),math.rad(0),math.rad(0))
A1 = Instance.new('Attachment',wed)
A1.Position = vt(0,2,0.25)

tr6 = Instance.new('Trail',wed)
tr6.Attachment0 = A1
tr6.Attachment1 = A0
tr6.Texture = "rbxassetid://2108945559"
tr6.LightEmission = 1
tr6.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
tr6.Color = ColorSequence.new(BrickColor.new('Really red').Color)
tr6.Lifetime = 0.6

tr4.Enabled = false
tr5.Enabled = false
tr6.Enabled = false

task.spawn(function() --end
FPWeld.Part1 = lwing2
FPWeld2.Part1 = lwing3
FPWeld3.Part1 = rwing2
FPWeld4.Part1 = rwing3
FPWeld5.Part1 = rwing1
end)

---- HERES THE RING


--[[ran = CreateParta(m2,0,0,"SmoothPlastic",wepcolor)
CreateMesh(ran,"Wedge",1.02,1.02,1.02)
CreateWeld(ran,larm,ran,0,0.15,0,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
ran = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(ran,"Wedge",0.9,0.9,1.025)
CreateWeld(ran,larm,ran,0,0.155,0,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
ran = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(ran,"Wedge",1.025,0.9,0.9)
CreateWeld(ran,larm,ran,0,0.155,-0.025,math.rad(0),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))


gan = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(gan,"Brick",1.075,0.1,1.075)
CreateWeld(gan,larm,gan,0,0.5,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

gan = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(gan,"Brick",1.075,0.1,1.075)
CreateWeld(gan,larm,gan,0,0.75,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))



gan = CreateParta(m2,0,0,"Neon",halocolor2)
CreateMesh(gan,"Brick",1.095,0.035,1.095)
CreateWeld(gan,larm,gan,0,0.5,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

gan = CreateParta(m2,0,0,"Neon",halocolor2)
CreateMesh(gan,"Brick",1.095,0.035,1.095)
CreateWeld(gan,larm,gan,0,0.75,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

gane = CreateParta(m3,0,0,"SmoothPlastic",lunacolor2)
CreateMesh(gane,"Brick",1.0625,0.2,1.0625)
CreateWeld(gane,larm,gane,0,0.6,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

star = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateSpecialMesh(star,"http://www.roblox.com/asset/?id=45428961",2.5,2.5,2.5)
CreateWeld(star,larm,star,0,0.475,0.6,math.rad(90),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
starl = CreateParta(m3,0,0,"SmoothPlastic",starcolor)
CreateSpecialMesh(starl,"http://www.roblox.com/asset/?id=45428961",1.95,2.55,1.95)
CreateWeld(starl,larm,starl,0,0.475,0.6,math.rad(90),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

--- second ring

ran = CreateParta(m2,0,0,"SmoothPlastic",wepcolor)
CreateMesh(ran,"Wedge",1.02,1.02,1.02)
CreateWeld(ran,rarm,ran,0,0.15,0,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
ran = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(ran,"Wedge",0.9,0.9,1.025)
CreateWeld(ran,rarm,ran,0,0.155,0,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
ran = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(ran,"Wedge",1.025,0.9,0.9)
CreateWeld(ran,rarm,ran,0,0.155,-0.025,math.rad(0),math.rad(-90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

gan = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(gan,"Brick",1.075,0.1,1.075)
CreateWeld(gan,rarm,gan,0,0.5,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

gan = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateMesh(gan,"Brick",1.075,0.1,1.075)
CreateWeld(gan,rarm,gan,0,0.75,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))



gan = CreateParta(m2,0,0,"Neon",halocolor2)
CreateMesh(gan,"Brick",1.095,0.035,1.095)
CreateWeld(gan,rarm,gan,0,0.5,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

gan = CreateParta(m2,0,0,"Neon",halocolor2)
CreateMesh(gan,"Brick",1.095,0.035,1.095)
CreateWeld(gan,rarm,gan,0,0.75,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

gane = CreateParta(m3,0,0,"SmoothPlastic",lunacolor2)
CreateMesh(gane,"Brick",1.0625,0.2,1.0625)
CreateWeld(gane,rarm,gane,0,0.6,0,math.rad(0),math.rad(0),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))

star = CreateParta(m,0,0,"SmoothPlastic",wepcolor)
CreateSpecialMesh(star,"http://www.roblox.com/asset/?id=45428961",2.5,2.5,2.5)
CreateWeld(star,rarm,star,0,-0.475,0.6,math.rad(90),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))
starl = CreateParta(m3,0,0,"SmoothPlastic",starcolor)
CreateSpecialMesh(starl,"http://www.roblox.com/asset/?id=45428961",1.95,2.55,1.95)
CreateWeld(starl,rarm,starl,0,-0.475,0.6,math.rad(90),math.rad(90),math.rad(0),0,0,0,math.rad(0),math.rad(0),math.rad(0))]]--



for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = BrickColor.new("Really black")
v.Material = "Glass"
end
end
for i, v in pairs(m2:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = BrickColor.new("Crimson")
v.Material = "Granite"
end
end
for i, v in pairs(m3:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = BrickColor.new("Really red")
v.Material = "Neon"
end
end
for i, v in pairs(mw2:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = BrickColor.new("Really red")
v.Material = "Neon"
end
end
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Really red")
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("White")
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("White")
v.Material = "Neon"
end
end
------


function RemoveOutlines(part)
  part.TopSurface, part.BottomSurface, part.LeftSurface, part.RightSurface, part.FrontSurface, part.BackSurface = 10, 10, 10, 10, 10, 10
end
function CreatePart(Parent, Material, Reflectance, Transparency, BColor, Name, Size)
  local Part = Create("Part")({
    Parent = Parent,
    Reflectance = Reflectance,
    Transparency = Transparency,
    CanCollide = false,
    Locked = true,
    BrickColor = BrickColor.new(tostring(BColor)),
    Name = Name,
    Size = Size,
    Material = Material
  })
  Part.CustomPhysicalProperties = PhysicalProperties.new(0.001, 0.001, 0.001, 0.001, 0.001)
  RemoveOutlines(Part)
  return Part
end
function CreateMesha(Mesh, Part, MeshType, MeshId, OffSet, Scale)
  local Msh = Create(Mesh)({
    Parent = Part,
    Offset = OffSet,
    Scale = Scale
  })
  if Mesh == "SpecialMesh" then
    Msh.MeshType = MeshType
    Msh.MeshId = MeshId
  end
  return Msh
end
function CreateWeld(Parent, Part0, Part1, C0, C1)
  local Weld = Create("Weld")({
    Parent = Parent,
    Part0 = Part0,
    Part1 = Part1,
    C0 = C0,
    C1 = C1
  })
  return Weld
end

Character=Player.Character 
PlayerGui=Player.PlayerGui 
Backpack=Player.Backpack 
Torso=Character.Torso 
Head=Character.Head 
Humanoid=Character.Humanoid
m=Instance.new('Model',Character)
LeftArm=Character["Left Arm"] 
LeftLeg=Character["Left Leg"] 
RightArm=Character["Right Arm"] 
RightLeg=Character["Right Leg"] 
LS=Torso["Left Shoulder"] 
LH=Torso["Left Hip"] 
RS=Torso["Right Shoulder"] 
RH=Torso["Right Hip"] 
Face = Head.face
Neck=Torso.Neck
it=Instance.new
attacktype=1
vt=Vector3.new
cf=CFrame.new
euler=CFrame.fromEulerAnglesXYZ
angles=CFrame.Angles
cloaked=false
necko=cf(0, 1, 0, -1, -0, -0, 0, 0, 1, 0, 1, 0)
necko2=cf(0, -0.5, 0, -1, -0, -0, 0, 0, 1, 0, 1, 0)
LHC0=cf(-1,-1,0,-0,-0,-1,0,1,0,1,0,0)
LHC1=cf(-0.5,1,0,-0,-0,-1,0,1,0,1,0,0)
RHC0=cf(1,-1,0,0,0,1,0,1,0,-1,-0,-0)
RHC1=cf(0.5,1,0,0,0,1,0,1,0,-1,-0,-0)
RootPart=Character.HumanoidRootPart
RootJoint=RootPart.RootJoint
RootCF=euler(-1.57,0,3.14)
attack = false 
attackdebounce = false 
deb=false
equipped=true
hand=false
MMouse=nil
combo=0
mana=0
trispeed=.2
attackmode='none'
local idle=0
local Anim="Idle"
local Effects={}
local gun=false
local shoot=false
local sine = 0
local change = 1


function RecolorTextAndRename(name,col1,col2,font)
modet.TextStrokeColor3 = col2
modet.TextColor3 = col1
modet.Font = font
modet.Text = name
techc.ImageColor3 = col2
circl.ImageColor3 = col2
circl2.ImageColor3 = col1
imgl2.ImageColor3 = col1
imgl2b.ImageColor3 = col2
ned.Text = name
ned.TextColor3 = col1
ned.TextStrokeColor3 = col2
end

local disably = false

local r = 255
local g = 0
local b = 0
coroutine.resume(coroutine.create(function()
while wait() do
	for i = 0, 254/5 do
		swait()
		g = g + 5
	end
	for i = 0, 254/5 do
		swait()
		r = r - 5
	end
	for i = 0, 254/5 do
		swait()
		b = b + 5
	end
	for i = 0, 254/5 do
		swait()
		g = g - 5
	end
	for i = 0, 254/5 do
		swait()
		r = r + 5
	end
	for i = 0, 254/5 do
		swait()
		b = b - 5
	end
end
end))

function warnedpeople(text,represfont,color,color2)
	if disably ~= true then
CFuncs["Sound"].Create("rbxassetid://534859368", char, 1.25,1)
CFuncs["Sound"].Create("rbxassetid://963718869", char, 0.8,1)
for i,v in pairs(game:GetService("Players"):GetPlayers()) do
coroutine.resume(coroutine.create(function()
if v.PlayerGui:FindFirstChild("Spinny")~= nil then
v.PlayerGui:FindFirstChild("Spinny"):destroy()
end
local scrg = Instance.new("ScreenGui",v.PlayerGui)
scrg.ResetOnSpawn = false
scrg.Name = "Spinny"
local frm = Instance.new("Frame",scrg)
frm.BackgroundTransparency = 0.25
frm.BackgroundColor3 = color
frm.BorderSizePixel = 0
frm.Rotation = 45
frm.Size = UDim2.new(3,0,0,100)
frm.Position = UDim2.new(-4,0,0,0)
local frm2 = frm:Clone()
frm2.Parent = scrg
frm2.BackgroundColor3 = color2
frm2.Position = UDim2.new(-4.05,0,0,0)
local imlb = Instance.new("ImageLabel",scrg)
imlb.BackgroundTransparency = 1
imlb.BackgroundColor3 = Color3.new(0,0,0)
imlb.Image = "rbxassetid://2344851144"
imlb.Size = UDim2.new(0,750,0,750)
imlb.ImageColor3 = color2
imlb.ImageTransparency = 0.25
imlb.Position = UDim2.new(-2.5,0,-2.5,0)
local imlb2 = imlb:Clone()
imlb2.Image = "rbxassetid://2076458450"
imlb2.Size = UDim2.new(1,0,1,0)
imlb2.ImageColor3 = color
imlb2.ImageTransparency = 0
imlb2.Position = UDim2.new(0,0,0,0)
local imlb3 = imlb:Clone()
imlb3.Image = "rbxassetid://2312119891"
imlb3.Size = UDim2.new(1,0,1,0)
imlb3.ImageColor3 = color2
imlb3.ImageTransparency = 0
imlb3.Position = UDim2.new(0,0,0,0)
local imlb4 = imlb:Clone()
imlb4.Image = "rbxassetid://2092248396"
imlb4.Size = UDim2.new(3,0,3,0)
imlb3.ImageColor3 = color
imlb4.ImageTransparency = 0
imlb4.Position = UDim2.new(-1,0,-1,0)
local imlb5 = imlb:Clone()
imlb5.Image = "rbxassetid://2344870656"
imlb5.Size = UDim2.new(10,0,10,0)
imlb5.ImageColor3 = color2
imlb5.ImageTransparency = 0
imlb5.Position = UDim2.new(-4.5,0,-4.5,0)
imlb2.Parent = imlb
imlb3.Parent = imlb
imlb4.Parent = imlb
imlb5.Parent = imlb
local txtlb2 = Instance.new("TextLabel",imlb)
coroutine.resume(coroutine.create(function()
while true do
swait()
if Error == false then
txtlb2.Text = text
elseif Error == true then
txtlb2.Text = est[math.random(1,17)]
end
end
end))
txtlb2.Font = represfont
txtlb2.TextColor3 = color
txtlb2.TextStrokeTransparency = 0
txtlb2.BackgroundTransparency = 1
txtlb2.TextStrokeColor3 = color2
txtlb2.TextScaled = true
txtlb2.Size = UDim2.new(1,0,1,0)
txtlb2.Position = UDim2.new(0,0,0,0)
local fvalen = 0.55
local fval = -0.49
coroutine.resume(coroutine.create(function()
while true do
swait()
if unstablemode == true then
imlb.ImageColor3 = Color3.new(math.random(0.5,1),0,0)
frm.BackgroundColor3 = Color3.new(math.random(0.5,1),0,0)
frm2.BackgroundColor3 = Color3.new(math.random(0.5,1),0,0)
imlb4.ImageColor3 = Color3.new(math.random(0.5,1),0,0)
txtlb2.TextStrokeColor3 = Color3.new(math.random(0.5,1),0,0)
txtlb2.TextColor3 = Color3.new(1,1,1)
imlb.ImageColor3 = Color3.new(math.random(0.5,1),0,0)
imlb3.ImageColor3 = Color3.new(math.random(0.5,1),0,0)
imgl2b.ImageColor3 = Color3.new(math.random(0.5,1),0,0)
frm.BackgroundColor3 = Color3.new(math.random(0.5,1),0,0)
frm2.BackgroundColor3 = Color3.new(math.random(0.5,1),0,0)
if math.random(1,12) == 1 then
imlb.ImageColor3 = Color3.new(0,0,0)
frm.BackgroundColor3 = Color3.new(0,0,0)
frm2.BackgroundColor3 = Color3.new(0,0,0)
txtlb2.TextStrokeColor3 = Color3.new(0,0,0)
txtlb2.TextColor3 = Color3.new(math.random(0.2,1),0,0)
imlb4.ImageColor3 = Color3.new(0,0,0)
imlb.ImageColor3 = Color3.new(0,0,0)
imlb3.ImageColor3 = Color3.new(0,0,0)
imgl2b.ImageColor3 = Color3.new(0,0,0)
frm.BackgroundColor3 = Color3.new(0,0,0)
frm2.BackgroundColor3 = Color3.new(0,0,0)
end
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if rainbowmode == true then
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.TextStrokeColor3 = Color3.new(r/255,g/255,b/255)
txtlb2.TextColor3 = Color3.new(r/255,g/255,b/255)
imlb.ImageColor3 = Color3.new(r/255,g/255,b/255)
frm.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
frm2.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
txtlb2.TextStrokeColor3 = Color3.new(1,1,1)
txtlb2.TextColor3 = Color3.new(r/255,g/255,b/255)
imlb.ImageColor3 = Color3.new(r/255,g/255,b/255)
imlb3.ImageColor3 = Color3.new(r/255,g/255,b/255)
imgl2b.ImageColor3 = Color3.new(r/255,g/255,b/255)
frm.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
frm2.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if Error == true then
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.TextStrokeColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
txtlb2.TextColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb2.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb3.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb4.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb5.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imgl2b.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
frm.BackgroundColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
frm2.BackgroundColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
text = est[math.random(1,17)]
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if CRAZED == true then
txtlb2.Rotation = math.random(-1,1)
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.TextStrokeColor3 = Color3.new(0,0,math.random(0.1,1))
txtlb2.TextColor3 = Color3.new(0,0,math.random(0,0.2))
imlb.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb2.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb3.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb4.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb5.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imgl2b.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
frm.BackgroundColor3 = Color3.new(0,0,math.random(0.1,1))
frm2.BackgroundColor3 = Color3.new(0,0,math.random(0.1,1))
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if chaosmode == true then
txtlb2.Rotation = math.random(-1,1)
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
imlb.ImageColor3 = BrickColor.random().Color
txtlb2.TextStrokeColor3 = BrickColor.random().Color
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if scrg.Parent ~= nil then
	fvalen = fvalen - 0.0001
elseif scrg.Parent == nil then
break
end
end
end))
local flol = -5
local flil = 1.6
coroutine.resume(coroutine.create(function()
	for i = 0, 49 do
		swait()
		flol = flol + 0.125
		flil = flil - 0.1
		frm.Size = frm.Size + UDim2.new(0.1,0,0,0)
		frm.Rotation = frm.Rotation - 0.25
		frm2.Size = frm2.Size + UDim2.new(0.1,0,0,0)
		frm2.Rotation = frm.Rotation + 0.325
		imlb3.Rotation = imlb3.Rotation - 10
		imlb2.Rotation = imlb.Rotation + 7.5
		imlb.Rotation = imlb.Rotation + 5
		if Error == false then
		txtlb2.Rotation = txtlb2.Rotation - 5.125
		elseif Error == true then
		txtlb2.Rotation = txtlb2.Rotation - 5.125 + math.random(-6,6)
		end
		imlb.Position = imlb.Position + UDim2.new(0.05125,0,0.04775,0)
	end
	for i = 0, 99 do
		swait()
		fval = fval + 0.05
		flol = flol + 0.005
		frm.Size = frm.Size + UDim2.new(0.005,0,0,0)
		frm.Rotation = frm.Rotation - 0.075
		frm2.Size = frm2.Size + UDim2.new(0.005,0,0,0)
		frm2.Rotation = frm2.Rotation + 0.125
		imlb3.Rotation = imlb3.Rotation - 2
		imlb2.Rotation = imlb.Rotation + 1.5
		imlb.Rotation = imlb.Rotation + 1
		if Error == false then
		txtlb2.Rotation = txtlb2.Rotation - 1.125
		elseif Error == true then
		txtlb2.Rotation = txtlb2.Rotation - 1.125 + math.random(-6,6)
		end
		imlb.Position = imlb.Position + UDim2.new(0.0015,0,0.00075,0)
	end
local valinc = 0
local vinc2 = 1
for i = 0, 99 do
swait()
vinc2 = vinc2 + 0.25
valinc = valinc + 0.0001
flol = flol + valinc
flil = flil + valinc
txtlb2.Rotation = txtlb2.Rotation - 1.125*vinc2
imlb3.Rotation = imlb3.Rotation - 2*vinc2
imlb.Rotation = imlb.Rotation + 1*vinc2
imlb.Position = imlb.Position + UDim2.new(0.0015*vinc2,0,0.0005*vinc2,0)
frm.Size = frm.Size + UDim2.new(0.005*vinc2,0,0,0)
frm.Rotation = frm.Rotation + 0.1*vinc2
frm2.Size = frm2.Size + UDim2.new(0.005*vinc2,0,0,0)
frm2.Rotation = frm2.Rotation + 0.225*vinc2
frm2.BackgroundTransparency = frm2.BackgroundTransparency + 0.0075
frm.BackgroundTransparency = frm.BackgroundTransparency + 0.0075
imlb.ImageTransparency = imlb.ImageTransparency + 0.005
imlb2.ImageTransparency = imlb2.ImageTransparency + 0.01
imlb3.ImageTransparency = imlb3.ImageTransparency + 0.01
imlb4.ImageTransparency = imlb4.ImageTransparency + 0.01
imlb5.ImageTransparency = imlb4.ImageTransparency + 0.01
txtlb2.TextStrokeTransparency = txtlb2.TextStrokeTransparency + 0.01
txtlb2.TextTransparency = txtlb2.TextTransparency + 0.01
end
scrg:Destroy()
end))
end))
end
end
end

function TheoriesMSGfunc(text,color,C3)
for i,v in pairs(game:GetService("Players"):GetPlayers()) do
coroutine.resume(coroutine.create(function()
if v.PlayerGui:FindFirstChild("Dialog")~= nil then
v.PlayerGui:FindFirstChild("Dialog"):destroy()
end
local scrg = Instance.new("ScreenGui",v.PlayerGui)
CFuncs["EchoSound"].Create("rbxassetid://525200869", scrg, 0.5, 1,0,10,0.1,0.25,1)
scrg.Name = "Dialog"
local txtlb = Instance.new("TextLabel",scrg)
txtlb.Text = ""
txtlb.Font = "Bodoni"
txtlb.TextColor3 = Color3.new(0,0,0)
txtlb.TextStrokeTransparency = 1
txtlb.BackgroundTransparency = 1
txtlb.BackgroundColor3 = color
txtlb.TextStrokeColor3 = C3
txtlb.TextColor3 = color
txtlb.TextScaled = true
txtlb.Size = UDim2.new(1,0,1,0)
txtlb.TextXAlignment = "Left"
txtlb.Position = UDim2.new(0,0,6.75 + 1,0)
local txtlb2 = Instance.new("TextLabel",scrg)
txtlb2.Text = ""
txtlb2.Font = "Bodoni"
txtlb2.TextColor3 = Color3.new(0,0,0)
txtlb2.TextStrokeTransparency = 0
txtlb2.BackgroundTransparency = 0.96
txtlb2.BackgroundColor3 = C3
txtlb2.TextStrokeColor3 = color
txtlb2.TextScaled = true
txtlb2.Size = UDim2.new(0, 999999, 0, 999)
txtlb2.TextXAlignment = "Center"
txtlb2.Position = UDim2.new(0,0,0,0)
local Iml = Instance.new("ImageLabel",scrg)
Iml.BackgroundTransparency = 1
Iml.BorderSizePixel = 0
Iml.ImageTransparency = 0
Iml.ImageColor3 = C3
Iml.Position = UDim2.new(0,0,0.75 + 1,0)
Iml.Size = UDim2.new(0, 638.82, 0, 591.5)
Iml.Image = "rbxassetid://0"
local Iml1 = Instance.new("ImageLabel",scrg)
Iml1.BackgroundTransparency = 1
Iml1.BorderSizePixel = 0
Iml1.ImageTransparency = 0
Iml1.ImageColor3 = C3
Iml1.Position = UDim2.new(0,0,0.75 + 1,0)
Iml1.Size = UDim2.new(0, 638.82, 0, 591.5)
Iml1.Image = "rbxassetid://2312119891"
local Iml2 = Instance.new("ImageLabel",scrg)
Iml2.BackgroundTransparency = 1
Iml2.BorderSizePixel = 0
Iml2.ImageTransparency = 0
Iml2.ImageColor3 = color
Iml2.Position = UDim2.new(0,0,0.75 + 1,0)
Iml2.Size = UDim2.new(0, 638.82, 0, 591.5)
Iml2.Image = "rbxassetid://2312119891"
local txtlb = Instance.new("TextLabel",Iml)
txtlb.Text = text
txtlb.Font = "SciFi"
txtlb.TextColor3 = color
txtlb.TextStrokeTransparency = 0
txtlb.BackgroundTransparency = 1
txtlb.TextStrokeColor3 = color
txtlb.TextScaled = true
txtlb.Size = UDim2.new(1,0,1,0)
txtlb.Position = UDim2.new(0,0,6.75 + 1,0)
local fvalen = 0.4
local fval = -0.49
coroutine.resume(coroutine.create(function()
while true do
swait()
if scrg.Parent ~= nil then
	fvalen = fvalen - 0.0001
elseif scrg.Parent == nil then
break
end
end
end))
local flol = -0.625
local flil = -0.935
coroutine.resume(coroutine.create(function()
	while true do
		swait()
		Iml1.Rotation = Iml1.Rotation + 2.5
		Iml2.Rotation = Iml2.Rotation - 2.5
	end
end))
coroutine.resume(coroutine.create(function()
	for i = 0, 36 do
		swait()
		fval = fval + 0.0125
		flol = flol + 0.025
		flil = flil + 0.025
		txtlb.Text = text
		txtlb2.BackgroundTransparency = txtlb2.BackgroundTransparency - 0.008
		txtlb.Position = UDim2.new(flil,0,0,0)
		Iml.Position = UDim2.new(flol,0,-0.05,0)
		Iml1.Position = UDim2.new(flol,0,-0.05,0)
		Iml2.Position = UDim2.new(flol,0,-0.05,0)
	end
coroutine.resume(coroutine.create(function()
		for i = 0, 99 do
		swait()
		fval = fval + 0.00001
		flol = flol + 0.0001
		flil = flil + 0.0001
		txtlb.Text = text
		txtlb.Position = UDim2.new(flil,0,0,0)
		Iml1.Position = UDim2.new(flol,0,-0.05,0)
		Iml1.Position = UDim2.new(flol,0,-0.05,0)
		Iml2.Position = UDim2.new(flol,0,-0.05,0)
	end
end))
wait(1.6)
local valinc = 0
for i = 0, 99 do
swait()
valinc = valinc + 0.0001
flol = flol + valinc
flil = flil + valinc
Iml1.Rotation = Iml1.Rotation - valinc*50
Iml.Rotation = Iml1.Rotation - valinc*2
Iml2.Rotation = Iml2.Rotation - valinc*50
Iml.Position = UDim2.new(flol,0,0,0)
Iml1.Position = UDim2.new(flol,0,-0.05,0)
Iml2.Position = UDim2.new(flol,0,-0.05,0)
txtlb.TextStrokeTransparency = txtlb.TextStrokeTransparency + 0.01
txtlb2.BackgroundTransparency = txtlb2.BackgroundTransparency + 0.006
txtlb.TextTransparency = txtlb.TextTransparency + 0.01
Iml.ImageTransparency = Iml1.ImageTransparency + 0.01
Iml1.ImageTransparency = Iml1.ImageTransparency + 0.01
Iml2.ImageTransparency = Iml2.ImageTransparency + 0.01
txtlb.BackgroundTransparency = txtlb.BackgroundTransparency + 0.0025
end
scrg:Destroy()
end))
end))
end
end

function IdolsWarn(text,represfont,color,color2)
	if disably ~= true then
CFuncs["Sound"].Create("rbxassetid://534859368", char, 1.25,1)
CFuncs["Sound"].Create("rbxassetid://963718869", char, 0.8,1)
for i,v in pairs(game:GetService("Players"):GetPlayers()) do
coroutine.resume(coroutine.create(function()
if v.PlayerGui:FindFirstChild("Spinny")~= nil then
v.PlayerGui:FindFirstChild("Spinny"):destroy()
end
local scrg = Instance.new("ScreenGui",v.PlayerGui)
scrg.ResetOnSpawn = false
scrg.Name = "Spinny"
local frm = Instance.new("Frame",scrg)
frm.BackgroundTransparency = 0.25
frm.BackgroundColor3 = color
frm.BorderSizePixel = 0
frm.Rotation = 45
frm.Size = UDim2.new(3,0,0,100)
frm.Position = UDim2.new(-4,0,0,0)
local frm2 = frm:Clone()
frm2.Parent = scrg
frm2.BackgroundColor3 = color2
frm2.Position = UDim2.new(-4.05,0,0,0)
local imlb = Instance.new("ImageLabel",scrg)
imlb.BackgroundTransparency = 1
imlb.BackgroundColor3 = color
imlb.Image = "rbxassetid://787198541"
imlb.Size = UDim2.new(0,750,0,750)
imlb.ImageColor3 = color2
imlb.ImageTransparency = 0.25
imlb.Position = UDim2.new(-2.5,0,-2.5,0)
local imlb2 = imlb:Clone()
imlb2.Image = "rbxassetid://197468643"
imlb2.Size = UDim2.new(1,0,1,0)
imlb2.ImageTransparency = 0
imlb2.Position = UDim2.new(0,0,0,0)
local imlb3 = imlb:Clone()
imlb3.Image = "rbxassetid://787191999"
imlb3.Size = UDim2.new(1,0,1,0)
imlb3.ImageColor3 = color2
imlb3.ImageTransparency = 0
imlb3.Position = UDim2.new(0,0,0,0)
local imlb4 = imlb:Clone()
imlb4.Image = "rbxassetid://2092248396"
imlb4.Size = UDim2.new(3,0,3,0)
imlb3.ImageColor3 = color
imlb4.ImageTransparency = 0
imlb4.Position = UDim2.new(-1,0,-1,0)
local imlb5 = imlb:Clone()
imlb5.Image = "rbxassetid://2344870656"
imlb5.Size = UDim2.new(10,0,10,0)
imlb5.ImageColor3 = color2
imlb5.ImageTransparency = 0
imlb5.Position = UDim2.new(-4.5,0,-4.5,0)
imlb2.Parent = imlb
imlb3.Parent = imlb
imlb4.Parent = imlb
imlb5.Parent = imlb
local txtlb2 = Instance.new("TextLabel",imlb)
coroutine.resume(coroutine.create(function()
while true do
swait()
if Error == false then
txtlb2.Text = text
elseif Error == true then
txtlb2.Text = est[math.random(1,17)]
end
end
end))
txtlb2.Font = represfont
txtlb2.TextColor3 = color
txtlb2.TextStrokeTransparency = 0
txtlb2.BackgroundTransparency = 1
txtlb2.TextStrokeColor3 = color2
txtlb2.TextScaled = true
txtlb2.Size = UDim2.new(1,0,1,0)
txtlb2.Position = UDim2.new(0,0,0,0)
local fvalen = 0.55
local fval = -0.49
coroutine.resume(coroutine.create(function()
while true do
swait()
if rainbowmode == true then
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.TextStrokeColor3 = Color3.new(r/255,g/255,b/255)
txtlb2.TextColor3 = Color3.new(r/255,g/255,b/255)
imlb.ImageColor3 = Color3.new(r/255,g/255,b/255)
frm.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
frm2.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
txtlb2.TextStrokeColor3 = Color3.new(1,1,1)
txtlb2.TextColor3 = Color3.new(r/255,g/255,b/255)
imlb.ImageColor3 = Color3.new(r/255,g/255,b/255)
imlb3.ImageColor3 = Color3.new(r/255,g/255,b/255)
imgl2b.ImageColor3 = Color3.new(r/255,g/255,b/255)
frm.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
frm2.BackgroundColor3 = Color3.new(r/255,g/255,b/255)
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if Error == true then
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.TextStrokeColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
txtlb2.TextColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb2.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb3.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb4.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imlb5.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
imgl2b.ImageColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
frm.BackgroundColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
frm2.BackgroundColor3 = Color3.new(math.random(0,1),math.random(0,1),math.random(0,1))
text = est[math.random(1,17)]
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if CRAZED == true then
txtlb2.Rotation = math.random(-1,1)
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.TextStrokeColor3 = Color3.new(0,0,math.random(0.1,1))
txtlb2.TextColor3 = Color3.new(0,0,math.random(0,0.2))
imlb.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb2.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb3.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb4.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imlb5.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
imgl2b.ImageColor3 = Color3.new(0,0,math.random(0.1,1))
frm.BackgroundColor3 = Color3.new(0,0,math.random(0.1,1))
frm2.BackgroundColor3 = Color3.new(0,0,math.random(0.1,1))
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if chaosmode == true then
txtlb2.Rotation = math.random(-1,1)
imlb.Position = imlb.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
txtlb2.Position = txtlb2.Position + UDim2.new(0,math.random(-1,1)/5,0,math.random(-1,1)/5)
imlb.ImageColor3 = BrickColor.random().Color
txtlb2.TextStrokeColor3 = BrickColor.random().Color
end
end
end))
coroutine.resume(coroutine.create(function()
while true do
swait()
if scrg.Parent ~= nil then
	fvalen = fvalen - 0.0001
elseif scrg.Parent == nil then
break
end
end
end))
local flol = -5
local flil = 1.6
coroutine.resume(coroutine.create(function()
	for i = 0, 49 do
		swait()
		flol = flol + 0.125
		flil = flil - 0.1
		frm.Size = frm.Size + UDim2.new(0.1,0,0,0)
		frm.Rotation = frm.Rotation - 0.25
		frm2.Size = frm2.Size + UDim2.new(0.1,0,0,0)
		frm2.Rotation = frm.Rotation + 0.325
		imlb3.Rotation = imlb3.Rotation - 10
		imlb2.Rotation = imlb.Rotation + 7.5
		imlb.Rotation = imlb.Rotation + 5
		if Error == false then
		txtlb2.Rotation = txtlb2.Rotation - 5.125
		elseif Error == true then
		txtlb2.Rotation = txtlb2.Rotation - 5.125 + math.random(-6,6)
		end
		imlb.Position = imlb.Position + UDim2.new(0.05125,0,0.04775,0)
	end
	for i = 0, 99 do
		swait()
		fval = fval + 0.05
		flol = flol + 0.005
		frm.Size = frm.Size + UDim2.new(0.005,0,0,0)
		frm.Rotation = frm.Rotation - 0.075
		frm2.Size = frm2.Size + UDim2.new(0.005,0,0,0)
		frm2.Rotation = frm2.Rotation + 0.125
		imlb3.Rotation = imlb3.Rotation - 2
		imlb2.Rotation = imlb.Rotation + 1.5
		imlb.Rotation = imlb.Rotation + 1
		if Error == false then
		txtlb2.Rotation = txtlb2.Rotation - 1.125
		elseif Error == true then
		txtlb2.Rotation = txtlb2.Rotation - 1.125 + math.random(-6,6)
		end
		imlb.Position = imlb.Position + UDim2.new(0.0015,0,0.00075,0)
	end
local valinc = 0
local vinc2 = 1
for i = 0, 99 do
swait()
vinc2 = vinc2 + 0.25
valinc = valinc + 0.0001
flol = flol + valinc
flil = flil + valinc
txtlb2.Rotation = txtlb2.Rotation - 1.125*vinc2
imlb3.Rotation = imlb3.Rotation - 2*vinc2
imlb.Rotation = imlb.Rotation + 1*vinc2
imlb.Position = imlb.Position + UDim2.new(0.0015*vinc2,0,0.0005*vinc2,0)
frm.Size = frm.Size + UDim2.new(0.005*vinc2,0,0,0)
frm.Rotation = frm.Rotation + 0.1*vinc2
frm2.Size = frm2.Size + UDim2.new(0.005*vinc2,0,0,0)
frm2.Rotation = frm2.Rotation + 0.225*vinc2
frm2.BackgroundTransparency = frm2.BackgroundTransparency + 0.0075
frm.BackgroundTransparency = frm.BackgroundTransparency + 0.0075
imlb.ImageTransparency = imlb.ImageTransparency + 0.005
imlb2.ImageTransparency = imlb2.ImageTransparency + 0.01
imlb3.ImageTransparency = imlb3.ImageTransparency + 0.01
imlb4.ImageTransparency = imlb4.ImageTransparency + 0.01
imlb5.ImageTransparency = imlb4.ImageTransparency + 0.01
txtlb2.TextStrokeTransparency = txtlb2.TextStrokeTransparency + 0.01
txtlb2.TextTransparency = txtlb2.TextTransparency + 0.01
end
scrg:Destroy()
end))
end))
end
end
end


--save shoulders 
RSH, LSH=nil, nil 
--welds 
RW, LW=Instance.new("Weld"), Instance.new("Weld") 
RW.Name="Right Shoulder" LW.Name="Left Shoulder"
LH=Torso["Left Hip"]
RH=Torso["Right Hip"]
TorsoColor=Torso.BrickColor
function NoOutline(Part)
Part.TopSurface,Part.BottomSurface,Part.LeftSurface,Part.RightSurface,Part.FrontSurface,Part.BackSurface = 10,10,10,10,10,10
end
ch=Character
RSH=ch.Torso["Right Shoulder"] 
LSH=ch.Torso["Left Shoulder"] 
-- 
RSH.Parent=nil 
LSH.Parent=nil 
-- 
RW.Name="Right Shoulder"
RW.Part0=ch.Torso 
RW.C0=cf(1.5, 0.5, 0) --* CFrame.fromEulerAnglesXYZ(1.3, 0, -0.5) 
RW.C1=cf(0, 0.5, 0) 
RW.Part1=ch["Right Arm"] 
RW.Parent=ch.Torso 
-- 
LW.Name="Left Shoulder"
LW.Part0=ch.Torso 
LW.C0=cf(-1.5, 0.5, 0) --* CFrame.fromEulerAnglesXYZ(1.7, 0, 0.8) 
LW.C1=cf(0, 0.5, 0) 
LW.Part1=ch["Left Arm"] 
LW.Parent=ch.Torso 

local Stats=Instance.new("BoolValue")
Stats.Name="Stats"
Stats.Parent=Character
local Atk=Instance.new("NumberValue")
Atk.Name="Damage"
Atk.Parent=Stats
Atk.Value=1
local Def=Instance.new("NumberValue")
Def.Name="Defense"
Def.Parent=Stats
Def.Value=1
local Speed=Instance.new("NumberValue")
Speed.Name="Speed"
Speed.Parent=Stats
Speed.Value=1
local Mvmt=Instance.new("NumberValue")
Mvmt.Name="Movement"
Mvmt.Parent=Stats
Mvmt.Value=1

local donum=0
 

function part(formfactor,parent,reflectance,transparency,brickcolor,name,size)
local fp=it("Part")
fp.formFactor=formfactor 
fp.Parent=parent
fp.Reflectance=reflectance
fp.Transparency=transparency
fp.CanCollide=false 
fp.Locked=true
fp.BrickColor=brickcolor
fp.Name=name
fp.Size=size
fp.Position=Torso.Position 
NoOutline(fp)
fp.Material="SmoothPlastic"
fp:BreakJoints()
return fp 
end 
 
function mesh(Mesh,part,meshtype,meshid,offset,scale)
local mesh=it(Mesh) 
mesh.Parent=part
if Mesh=="SpecialMesh" then
mesh.MeshType=meshtype
if meshid~="nil" then
mesh.MeshId="http://www.roblox.com/asset/?id="..meshid
end
end
mesh.Offset=offset
mesh.Scale=scale
return mesh
end
 
function weld(parent,part0,part1,c0)
local weld=it("Weld") 
weld.Parent=parent
weld.Part0=part0 
weld.Part1=part1 
weld.C0=c0
return weld
end
 
local Color1=Torso.BrickColor

local bodvel=Instance.new("BodyVelocity")
local bg=Instance.new("BodyGyro")

--------- SazEreno's Artificial HB --------------
ArtificialHB = Instance.new("BindableEvent", script)
ArtificialHB.Name = "ArtificialHB"

script:WaitForChild("ArtificialHB")
Frame_Speed = 1 / 60
frame = Frame_Speed
tf = 0
allowframeloss = false
tossremainder = false
lastframe = tick()
script.ArtificialHB:Fire()

game:GetService("RunService").Heartbeat:connect(function(s, p)
	tf = tf + s
	if tf >= frame then
		if allowframeloss then
			script.ArtificialHB:Fire()
			lastframe = tick()
		else
			for i = 1, math.floor(tf / frame) do
				script.ArtificialHB:Fire()
			end
		lastframe = tick()
		end
		if tossremainder then
			tf = 0
		else
			tf = tf - frame * math.floor(tf / frame)
		end
	end
end)

------------------
function swait(num)
if num == 0 or num == nil then
		ArtificialHB.Event:wait()
	else
		for i = 1, num do
			ArtificialHB.Event:wait()
		end
	end
end
-------- RAINBOW LEAVE IT TO ME
 
 
so = function(id,par,vol,pit) 
coroutine.resume(coroutine.create(function()
local sou = Instance.new("Sound",par or workspace)
sou.Volume=vol
sou.Pitch=pit or 1
sou.SoundId=id
swait() 
sou:play() 
game:GetService("Debris"):AddItem(sou,6)
end))
end
 
function clerp(a,b,t) 
local qa = {QuaternionFromCFrame(a)}
local qb = {QuaternionFromCFrame(b)} 
local ax, ay, az = a.x, a.y, a.z 
local bx, by, bz = b.x, b.y, b.z
local _t = 1-t
return QuaternionToCFrame(_t*ax + t*bx, _t*ay + t*by, _t*az + t*bz,QuaternionSlerp(qa, qb, t)) 
end 

function aclerp(startCF,endCF,alpha)
    return startCF:lerp(endCF, alpha)
end
 
function QuaternionFromCFrame(cf) 
local mx, my, mz, m00, m01, m02, m10, m11, m12, m20, m21, m22 = cf:components() 
local trace = m00 + m11 + m22 
if trace > 0 then 
local s = math.sqrt(1 + trace) 
local recip = 0.5/s 
return (m21-m12)*recip, (m02-m20)*recip, (m10-m01)*recip, s*0.5 
else 
local i = 0 
if m11 > m00 then
i = 1
end
if m22 > (i == 0 and m00 or m11) then 
i = 2 
end 
if i == 0 then 
local s = math.sqrt(m00-m11-m22+1) 
local recip = 0.5/s 
return 0.5*s, (m10+m01)*recip, (m20+m02)*recip, (m21-m12)*recip 
elseif i == 1 then 
local s = math.sqrt(m11-m22-m00+1) 
local recip = 0.5/s 
return (m01+m10)*recip, 0.5*s, (m21+m12)*recip, (m02-m20)*recip 
elseif i == 2 then 
local s = math.sqrt(m22-m00-m11+1) 
local recip = 0.5/s return (m02+m20)*recip, (m12+m21)*recip, 0.5*s, (m10-m01)*recip 
end 
end 
end
 
function QuaternionToCFrame(px, py, pz, x, y, z, w) 
local xs, ys, zs = x + x, y + y, z + z 
local wx, wy, wz = w*xs, w*ys, w*zs 
local xx = x*xs 
local xy = x*ys 
local xz = x*zs 
local yy = y*ys 
local yz = y*zs 
local zz = z*zs 
return CFrame.new(px, py, pz,1-(yy+zz), xy - wz, xz + wy,xy + wz, 1-(xx+zz), yz - wx, xz - wy, yz + wx, 1-(xx+yy)) 
end
 
function QuaternionSlerp(a, b, t) 
local cosTheta = a[1]*b[1] + a[2]*b[2] + a[3]*b[3] + a[4]*b[4] 
local startInterp, finishInterp; 
if cosTheta >= 0.0001 then 
if (1 - cosTheta) > 0.0001 then 
local theta = math.acos(cosTheta) 
local invSinTheta = 1/math.sin(theta) 
startInterp = math.sin((1-t)*theta)*invSinTheta 
finishInterp = math.sin(t*theta)*invSinTheta  
else 
startInterp = 1-t 
finishInterp = t 
end 
else 
if (1+cosTheta) > 0.0001 then 
local theta = math.acos(-cosTheta) 
local invSinTheta = 1/math.sin(theta) 
startInterp = math.sin((t-1)*theta)*invSinTheta 
finishInterp = math.sin(t*theta)*invSinTheta 
else 
startInterp = t-1 
finishInterp = t 
end 
end 
return a[1]*startInterp + b[1]*finishInterp, a[2]*startInterp + b[2]*finishInterp, a[3]*startInterp + b[3]*finishInterp, a[4]*startInterp + b[4]*finishInterp 
end

local function CFrameFromTopBack(at, top, back)
local right = top:Cross(back)
return CFrame.new(at.x, at.y, at.z,
right.x, top.x, back.x,
right.y, top.y, back.y,
right.z, top.z, back.z)
end

function Triangle(a, b, c)
local edg1 = (c-a):Dot((b-a).unit)
local edg2 = (a-b):Dot((c-b).unit)
local edg3 = (b-c):Dot((a-c).unit)
if edg1 <= (b-a).magnitude and edg1 >= 0 then
a, b, c = a, b, c
elseif edg2 <= (c-b).magnitude and edg2 >= 0 then
a, b, c = b, c, a
elseif edg3 <= (a-c).magnitude and edg3 >= 0 then
a, b, c = c, a, b
else
assert(false, "unreachable")
end
 
local len1 = (c-a):Dot((b-a).unit)
local len2 = (b-a).magnitude - len1
local width = (a + (b-a).unit*len1 - c).magnitude
 
local maincf = CFrameFromTopBack(a, (b-a):Cross(c-b).unit, -(b-a).unit)
 
local list = {}
 
if len1 > 0.01 then
local w1 = Instance.new('WedgePart', m)
game:GetService("Debris"):AddItem(w1,5)
w1.Material = "SmoothPlastic"
w1.FormFactor = 'Custom'
w1.BrickColor = BrickColor.new("Really red")
w1.Transparency = 0
w1.Reflectance = 0
w1.Material = "SmoothPlastic"
w1.CanCollide = false
local l1 = Instance.new("PointLight",w1)
l1.Color = Color3.new(170,0,0)
NoOutline(w1)
local sz = Vector3.new(0.2, width, len1)
w1.Size = sz
local sp = Instance.new("SpecialMesh",w1)
sp.MeshType = "Wedge"
sp.Scale = Vector3.new(0,1,1) * sz/w1.Size
w1:BreakJoints()
w1.Anchored = true
w1.Parent = workspace
w1.Transparency = 0.7
table.insert(Effects,{w1,"Disappear",.01})
w1.CFrame = maincf*CFrame.Angles(math.pi,0,math.pi/2)*CFrame.new(0,width/2,len1/2)
table.insert(list,w1)
end
 
if len2 > 0.01 then
local w2 = Instance.new('WedgePart', m)
game:GetService("Debris"):AddItem(w2,5)
w2.Material = "SmoothPlastic"
w2.FormFactor = 'Custom'
w2.BrickColor = BrickColor.new("Really red")
w2.Transparency = 0
w2.Reflectance = 0
w2.Material = "SmoothPlastic"
w2.CanCollide = false
local l2 = Instance.new("PointLight",w2)
l2.Color = Color3.new(170,0,0)
NoOutline(w2)
local sz = Vector3.new(0.2, width, len2)
w2.Size = sz
local sp = Instance.new("SpecialMesh",w2)
sp.MeshType = "Wedge"
sp.Scale = Vector3.new(0,1,1) * sz/w2.Size
w2:BreakJoints()
w2.Anchored = true
w2.Parent = workspace
w2.Transparency = 0.7
table.insert(Effects,{w2,"Disappear",.01})
w2.CFrame = maincf*CFrame.Angles(math.pi,math.pi,-math.pi/2)*CFrame.new(0,width/2,-len1 - len2/2)
table.insert(list,w2)
end
return unpack(list)
end
 

function Damagefunc(Part, hit, minim, maxim, knockback, Type, Property, Delay, HitSound, HitPitch)
 if FlingOnTouchh == true and ClickToFling ~= true then
  Flingy(Part.Parent)
 end
end
function ShowDamage(Pos, Text, Time, Color)
end
function MagniDamage(Part, magni, mindam, maxdam, knock, Type)
  for _, c in pairs(workspace:children()) do
    local hum = c:findFirstChildOfClass("Humanoid")
    if hum ~= nil then
      local head = c:findFirstChild("Head")
      if head ~= nil then
        local targ = head.Position - Part.Position
        local mag = targ.magnitude
        if magni >= mag and c.Name ~= Player.Name then
          Damagefunc(head, head, mindam, maxdam, knock, Type, RootPart, 0.1, "rbxassetid://231917784", 1)
        end
      end
    end
  end
end

function MagniDamageWithEffect(Part, magni, mindam, maxdam, knock, Type)
  for _, c in pairs(workspace:children()) do
    local hum = c:findFirstChild("Humanoid")
    if hum ~= nil then
      local head = c:findFirstChild("Torso")
      if head ~= nil then
        local targ = head.Position - Part.Position
        local mag = targ.magnitude
        if magni >= mag and c.Name ~= Player.Name then
	MagicBlock(BrickColor.new("Pastel light blue"),head.CFrame,5,5,5,1,1,1,0.05)
          Damagefunc(head, head, mindam, maxdam, knock, Type, RootPart, 0.1, "rbxassetid://231917784", 1)
        end
      end
    end
  end
end

function rayCast(Pos, Dir, Max, Ignore)  -- Origin Position , Direction, MaxDistance , IgnoreDescendants
return game:service("Workspace"):FindPartOnRay(Ray.new(Pos, Dir.unit * (Max or 999.999)), Ignore) 
end 

function tbeam(col1,col2)
	local lookavec = 0 
	local mult = 1
local elocacenter = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = root.CFrame
local eloca1 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca1)
at1.Position = vt(0,10,0)
local at2 = Instance.new("Attachment",eloca1)
at2.Position = vt(0,-10,0)
local trl = Instance.new('Trail',eloca1)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(col1.Color)
trl.Lifetime = 4
local eff = Instance.new("ParticleEmitter",eloca1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(col1.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,1)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca2 = eloca1:Clone()
eloca2.Parent = elocacenter
local eloca3 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca3.Anchored = true
eloca3.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca3)
at1.Position = vt(0,10,0)
local at2 = Instance.new("Attachment",eloca3)
at2.Position = vt(0,-10,0)
local trl = Instance.new('Trail',eloca3)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(col2.Color)
trl.Lifetime = 4
local eff = Instance.new("ParticleEmitter",eloca3)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(col2.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,1)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca4 = eloca3:Clone()
eloca4.Parent = elocacenter
elocacenter.CFrame = root.CFrame
sphere2(6,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),0.9,7,0.9,col2)
sphere2(5,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),0.8,7,0.8,col1)
sphere2(4,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),0.6,7,0.6,col2)
sphere2(3,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),0.5,7,0.5,col1)
local effx = Instance.new("ParticleEmitter",root)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(col1.Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
local effx2 = Instance.new("ParticleEmitter",root)
effx2.Texture = "rbxassetid://2273224484"
effx2.LightEmission = 1
effx2.Color = ColorSequence.new(col1.Color)
effx2.Rate = 500000
effx2.Lifetime = NumberRange.new(1,2)
effx2.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,200,0),NumberSequenceKeypoint.new(0.1,50,0),NumberSequenceKeypoint.new(0.8,25,0),NumberSequenceKeypoint.new(1,0,0)})
effx2.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx2.Speed = NumberRange.new(50,1200)
effx2.Drag = 5
effx2.Rotation = NumberRange.new(-500,500)
effx2.VelocitySpread = 9000
effx2.RotSpeed = NumberRange.new(-50,50)
local effx3 = effx2:Clone()
effx3.Parent = root
effx3.Color = ColorSequence.new(col2.Color)
symbolizeBlink(root,0,144580273,col1.Color,20,0,0,0,root,true,-5,2)
symbolizeBlink(root,0,144580273,col2.Color,25,0,0,0,root,true,-5,1)
for i = 0, 1 do
CFuncs["Sound"].Create("rbxassetid://763717897", root, 5, 1.25)
CFuncs["Sound"].Create("rbxassetid://1192402877", root, 5,0.75)
CFuncs["Sound"].Create("rbxassetid://1664711478", root, 2.5,1)
CFuncs["Sound"].Create("rbxassetid://763718160", root, 5, 0.75)
end
coroutine.resume(coroutine.create(function()
	wait(0.05)
	effx.Enabled = false
	wait(0.1)
	effx2.Enabled = false
	effx3.Enabled = false
end))
coroutine.resume(coroutine.create(function()
for i = 0, 9, 0.1 do
	swait()
	mult = mult + 0.5
lookavec = lookavec + 0.1*mult
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/10),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(40+lookavec/5,-15+lookavec*2,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.new(-40-lookavec/5,-15+lookavec*2,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.new(0,-15+lookavec*2,40+lookavec/5)
eloca4.CFrame = elocacenter.CFrame*CFrame.new(0,-15+lookavec*2,-40-lookavec/5)
end
for i,v in pairs(elocacenter:GetDescendants()) do
	if v:IsA("ParticleEmitter") then
		v.Enabled = false
	end
end
wait(6)
effx:Destroy()
effx2:Destroy()
effx3:Destroy()
elocacenter:Destroy()
end))
end

function SkullEffect(brickcolor,cframe,x1,y1,z1,delay)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe
local msh=mesh("SpecialMesh",prt,"FileMesh","http://www.roblox.com/asset/?id=4770583",vt(0,0,0),vt(x1,y1,z1))
--http://www.roblox.com/asset/?id=4770560
game:GetService("Debris"):AddItem(prt,2)
CF=prt.CFrame
coroutine.resume(coroutine.create(function(Part,Mesh,TehCF) 
for i=0,1,0.2 do
wait()
Part.CFrame=CF*cf(0,0,-0.4)
end
for i=0,1,delay do
wait()
--Part.CFrame=CF*cf((math.random(-1,0)+math.random())/5,(math.random(-1,0)+math.random())/5,(math.random(-1,0)+math.random())/5)
Mesh.Scale=Mesh.Scale
end
for i=0,1,0.1 do
wait()
Part.Transparency=i
end
Part.Parent=nil
end),prt,msh,CF)
end
 
function MagicBlock(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
prt.CFrame=prt.CFrame*euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
for i=0,1,delay do
swait()
Part.CFrame=Part.CFrame*euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end

function MagicBlockSteady(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay,rottype)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
	local rtype = rottype
for i=0,1,delay do
swait()
if rtype == 1 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,0.1)
elseif rtype == 2 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,-0.1)
end
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end

function MagicSphere(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe
prt.CFrame=prt.CFrame*euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))
msh=mesh("SpecialMesh",prt,"Sphere","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
for i=0,1,delay do
wait()
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end

function MagicBlockSteady(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay,rottype)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
	local rtype = rottype
for i=0,1,delay do
swait()
if rtype == 1 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,0.1)
elseif rtype == 2 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,-0.1)
end
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end

function MagicShock(brickcolor,cframe,x1,y1,x3,y3,delay,rottype)
local prt=part(3,char,1,1,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
local dec = decal(prt.Color,"http://www.roblox.com/asset/?id=874580939","Front",prt)
local dec2 = decal(prt.Color,"http://www.roblox.com/asset/?id=874580939","Front",prt)
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,0.01))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
	local rtype = rottype
for i=0,1,delay do
swait()
if rtype == 1 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,0.1)
elseif rtype == 2 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,-0.1)
end
dec.Transparency=i
dec2.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,0)
end
Part.Parent=nil
end),prt,msh)
end

function MagicShockAlt(brickcolor,cframe,x1,y1,x3,y3,delay,rottype)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,0.01))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
	local rtype = rottype
for i=0,1,delay do
swait()
if rtype == 1 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,0.1)
elseif rtype == 2 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,-0.1)
end
prt.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,0)
end
Part.Parent=nil
end),prt,msh)
end

function MagicShockAltCircle(brickcolor,cframe,x1,z1,x3,z3,delay,rottype)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
	local rtype = rottype
for i=0,1,delay do
swait()
if rtype == 1 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0.1,0)
elseif rtype == 2 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,-0.1,0)
end
prt.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,0,z3)
end
Part.Parent=nil
end),prt,msh)
end

function MagicShockTrailAlt(brickcolor,cframe,x1,y1,z1,x3,y3,delay,rottype)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
	local rtype = rottype
for i=0,1,delay do
swait()
if rtype == 1 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,0.1)
elseif rtype == 2 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,-0.1)
end
prt.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,0)
end
Part.Parent=nil
end),prt,msh)
end

function MagicShockTrailAlt2(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay,rottype)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.Material = "Neon"
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
	local rtype = rottype
for i=0,1,delay do
swait()
if rtype == 1 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,0.1)
elseif rtype == 2 then
prt.CFrame = prt.CFrame*CFrame.Angles(0,0,-0.1)
end
prt.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end
 
function MagicBlock2(brickcolor,cframe,Parent,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,char,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=false
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
local wld=weld(prt,prt,Parent,cframe)
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh,Weld) 
for i=0,1,delay do
wait()
Weld.C0=euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))*cframe
--Part.CFrame=Part.CFrame*euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh,wld)
end
 
function MagicBlock3(brickcolor,cframe,Parent,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=false
prt.CFrame=cframe
msh=mesh("BlockMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
local wld=weld(prt,prt,Parent,euler(0,0,0)*cf(0,0,0))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh,Weld) 
for i=0,1,delay do
wait()
Weld.C0=euler(i*20,0,0)
--Part.CFrame=Part.CFrame*euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh,wld)
end
 
function MagicCircle2(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe
local msh=mesh("CylinderMesh",prt,"","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,2)
coroutine.resume(coroutine.create(function(Part,Mesh) 
for i=0,1,delay do
wait()
Part.CFrame=Part.CFrame
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
local prt2=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt2.Anchored=true
prt2.CFrame=cframe*euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))
local msh2=mesh("SpecialMesh",prt2,"Sphere","",vt(0,0,0),vt(0.5,0.5,0.5))
game:GetService("Debris"):AddItem(prt2,2)
coroutine.resume(coroutine.create(function(Part,Mesh) 
for i=0,1,0.1 do
wait()
Part.CFrame=Part.CFrame*cf(0,0.5,0)
end
Part.Parent=nil
end),prt2,msh2)
end
for i=0,1,delay*2 do
wait()
Part.CFrame=Part.CFrame
Mesh.Scale=vt((x1+x3)-(x1+x3)*i,(y1+y3)-(y1+y3)*i,(z1+z3)-(z1+z3)*i)
end
Part.Parent=nil
end),prt,msh)
end
 
function MagicCircle(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe
local msh=mesh("SpecialMesh",prt,"Sphere","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,2)
coroutine.resume(coroutine.create(function(Part,Mesh) 
for i=0,1,delay do
wait()
Part.CFrame=Part.CFrame
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end
 
function BreakEffect(brickcolor,cframe,x1,y1,z1)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe*euler(math.random(-50,50),math.random(-50,50),math.random(-50,50))
local msh=mesh("SpecialMesh",prt,"Sphere","",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,2)
coroutine.resume(coroutine.create(function(Part,CF,Numbb,randnumb) 
CF=Part.CFrame
Numbb=0
randnumb=math.random()/10
rand1=math.random()/10
for i=0,1,rand1 do
wait()
CF=CF*cf(0,math.random()/2,0)
--Part.CFrame=Part.CFrame*euler(0.5,0,0)*cf(0,1,0)
Part.CFrame=CF*euler(Numbb,0,0)
Part.Transparency=i
Numbb=Numbb+randnumb
end
Part.Parent=nil
end),prt,CF,Numbb,randnumb)
end
 
function MagicWaveThing(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe
msh=mesh("SpecialMesh",prt,"FileMesh","http://www.roblox.com/asset/?id=1051557",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh) 
for i=0,1,delay do
wait()
Part.CFrame=Part.CFrame*euler(0,0.7,0)
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end
 
function WaveEffect(brickcolor,cframe,x1,y1,z1,x3,y3,z3,delay)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe
msh=mesh("SpecialMesh",prt,"FileMesh","http://www.roblox.com/asset/?id=20329976",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,2)
coroutine.resume(coroutine.create(function(Part,Mesh) 
for i=0,1,delay do
wait()
Part.CFrame=Part.CFrame*cf(0,y3/2,0)
Part.Transparency=i
Mesh.Scale=Mesh.Scale+vt(x3,y3,z3)
end
Part.Parent=nil
end),prt,msh)
end

function waveEff(bonuspeed,type,typeoftrans,pos,scale,value,value2,color)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
if typeoftrans == "In" then
rng.Transparency = 1
end
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "FileMesh"
rngm.MeshId = "rbxassetid://20329976"
rngm.Scale = scale
local scaler2 = 1
local scaler2b = 1
if type == "Add" then
scaler2 = 1*value
scaler2b = 1*value2
elseif type == "Divide" then
scaler2 = 1/value
scaler2b = 1/value2
end
local randomrot = math.random(1,2)
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
scaler2b = scaler2b - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
scaler2b = scaler2b - 0.01/value*bonuspeed
end
if randomrot == 1 then
rng.CFrame = rng.CFrame*CFrame.Angles(0,math.rad(5*bonuspeed/2),0)
elseif randomrot == 2 then
rng.CFrame = rng.CFrame*CFrame.Angles(0,math.rad(-5*bonuspeed/2),0)
end
if typeoftrans == "Out" then
rng.Transparency = rng.Transparency + 0.01*bonuspeed
elseif typeoftrans == "In" then
rng.Transparency = rng.Transparency - 0.01*bonuspeed
end
rngm.Scale = rngm.Scale + Vector3.new(scaler2*bonuspeed, scaler2b*bonuspeed, scaler2*bonuspeed)
end
rng:Destroy()
end))
end
 
function StravEffect(brickcolor,cframe,x,y,z,x1,y1,z1,delay)
local prt=part(3,workspace,0,0,brickcolor,"Effect",vt(0.5,0.5,0.5))
prt.Anchored=true
prt.CFrame=cframe*cf(x,y,z)
msh=mesh("SpecialMesh",prt,"FileMesh","rbxassetid://168892363",vt(0,0,0),vt(x1,y1,z1))
game:GetService("Debris"):AddItem(prt,5)
coroutine.resume(coroutine.create(function(Part,Mesh,ex,why,zee) 
local num=math.random()
local num2=math.random(-3,2)+math.random()
local numm=0
for i=0,1,delay*2 do
swait()
Part.CFrame=cframe*euler(0,numm*num*10,0)*cf(ex,why,zee)*cf(-i*10,num2,0)
Part.Transparency=i
numm=numm+0.01
end
Part.Parent=nil
Mesh.Parent=nil
end),prt,msh,x,y,z)
end

function dmgstart(dmg,what)
	hitcon = what.Touched:connect(function(hit)
		local hum = hit.Parent:FindFirstChildOfClass("Humanoid")
		local thisdudeisgoingtodie = hit.Parent
		if thisdudeisgoingtodie and not thisdudeisgoingtodie:IsDescendantOf(Character) then
			Flingy(thisdudeisgoingtodie)
		end
	end)
end

function dmgstop()
	hitcon:disconnect()
end

function Cloak()
Face.Parent=nil
cloaked=true
        for _,v in pairs(Torso.Parent:children()) do
                if v.className=="Part" and v.Name~="HumanoidRootPart" then
                coroutine.resume(coroutine.create(function() 
                for i=0,1,0.2 do
                wait()
                v.Transparency=i
                end
                v.Transparency=1
                end))
                end
                if v.className=="Hat" then
                hatp=v.Handle
                coroutine.resume(coroutine.create(function(derp) 
                for i=0,1,0.2 do
                wait()
                derp.Transparency=i
                end
                derp.Transparency=1
                end),hatp)
                end
        end
        for _,v in pairs(m:children()) do
                if v.className=="Part" then
                coroutine.resume(coroutine.create(function() 
                for i=0,1,0.2 do
                wait()
                v.Transparency=i
                end
                v.Transparency=1
                end))
                end
        end
end
 
function UnCloak()
so("http://roblox.com/asset/?id=2767090",Torso,1,1.1) 
Face.Parent=Head 
cloaked=false
        for _,v in pairs(Torso.Parent:children()) do
                if v.className=="Part" and v.Name~="HumanoidRootPart" then
                coroutine.resume(coroutine.create(function() 
                for i=0,1,0.1 do
                wait()
                v.Transparency=v.Transparency-0.1
                end
                v.Transparency=0
                end))
                end
                if v.className=="Hat" then
                hatp=v.Handle
                coroutine.resume(coroutine.create(function(derp) 
                for i=0,1,0.1 do
                wait()
                derp.Transparency=derp.Transparency-0.1
                end
                derp.Transparency=0
                end),hatp)
                end
        end
        for _,v in pairs(m:children()) do
                if v.className=="Part" and v.Name~="hitbox" and v.Name~='tip' then
                coroutine.resume(coroutine.create(function() 
                for i=0,1,0.1 do
                wait()
                v.Transparency=1
                end
                v.Transparency=1
                end))
                v.Transparency=1
                end
        end
end

local origcolor = BrickColor.new("Pastel light blue")
---- This section of explosions.

----


function ring(type,pos,scale,value)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = origcolor
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshId = "http://www.roblox.com/asset/?id=3270017"
rngm.Scale = scale
local scaler2 = 1
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value
end
rng.Transparency = rng.Transparency + 0.01
rngm.Scale = rngm.Scale + Vector3.new(scaler2, scaler2, 0)
end
rng:Destroy()
end))
end


function wave(type,pos,scale,value)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = origcolor
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshId = "http://www.roblox.com/asset/?id=20329976"
rngm.Scale = scale
local scaler2 = 1
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value
end
rng.Transparency = rng.Transparency + 0.01
rngm.Scale = rngm.Scale + Vector3.new(scaler2, scaler2, scaler2)
end
rng:Destroy()
end))
end

function wind(type,pos,scale,value,speed)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = origcolor
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshId = "http://www.roblox.com/asset/?id=1051557"
rngm.Scale = scale
local scaler2 = 1
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value
end
rng.CFrame = rng.CFrame*CFrame.Angles(0,0.025*speed,0)
rng.Transparency = rng.Transparency + 0.01
rngm.Scale = rngm.Scale + Vector3.new(scaler2, scaler2, scaler2)
end
rng:Destroy()
end))
end

function groundwind(type,pos,scale,value,speed)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = origcolor
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshId = "http://www.roblox.com/asset/?id=1051557"
rngm.Scale = scale
local scaler2 = 1
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value
end
rng.CFrame = rng.CFrame*CFrame.Angles(0,0.025*speed,0)
rng.Transparency = rng.Transparency + 0.01
rngm.Scale = rngm.Scale + Vector3.new(scaler2, scaler2/5, scaler2)
end
rng:Destroy()
end))
end

function CameraManager()
  if TwoD and not CamInterrupt then
    if Humanoid.Health > 0 then
      Camera.CameraSubject = Humanoid
      Camera.CameraType = "Scriptable"
      Humanoid.AutoRotate = false
      if Booleans.GyroUse then
        Directer.MaxTorque = Vec3(0, huge, 0)
      else
        Directer.MaxTorque = Vec3(0, 0, 0)
      end
      if TargetInfo[1] ~= nil and TargetInfo[2] ~= nil then
        if Booleans.CamFollow then
          CPart.CFrame = cFrame(RootPart.Position, Vec3(TargetInfo[1].Position.X, RootPart.Position.Y, TargetInfo[1].Position.Z))
          Directer.CFrame = cFrame((RootPart.CFrame * cFrame(0, 0, 10)).p, TargetInfo[1].Position)
        else
          CPart.Position = RootPart.Position
        end
      else
        local ahead = (RootPart.CFrame * cFrame(0, 0, -3)).p
        CPart.CFrame = cFrame(RootPart.Position, Vec3(ahead.X, RootPart.Position.Y, ahead.Z))
      end
      Camera.CFrame = lerp(Camera.CFrame, CPart.CFrame * cFrame(25, 3, 0) * Euler(0, radian(90), 0), 0.2)
    else
      Camera.CameraSubject = Humanoid
      Camera.CameraType = "Custom"
      Controller.Disabled = false
    end
  end
end

function ring(type,pos,scale,value)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = origcolor
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshId = "http://www.roblox.com/asset/?id=3270017"
rngm.Scale = scale
local scaler2 = 1
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value
end
rng.Transparency = rng.Transparency + 0.01
rngm.Scale = rngm.Scale + Vector3.new(scaler2, scaler2, 0)
end
rng:Destroy()
end))
end


function wave(type,pos,scale,value)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = origcolor
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshId = "http://www.roblox.com/asset/?id=20329976"
rngm.Scale = scale
local scaler2 = 1
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value
end
rng.Transparency = rng.Transparency + 0.01
rngm.Scale = rngm.Scale + Vector3.new(scaler2, scaler2, scaler2)
end
rng:Destroy()
end))
end

function sphere(bonuspeed,type,pos,scale,value,color)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Sphere"
rngm.Scale = scale
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
local scaler2 = 1
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
end
if chaosmode == true then
rng.BrickColor = BrickColor.random()
end
rng.Transparency = rng.Transparency + 0.01*bonuspeed
rngm.Scale = rngm.Scale + Vector3.new(scaler2*bonuspeed, scaler2*bonuspeed, scaler2*bonuspeed)
end
rng:Destroy()
end))
end

function sphere2(bonuspeed,type,pos,scale,value,value2,value3,color)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Sphere"
rngm.Scale = scale
local scaler2 = 1
local scaler2b = 1
local scaler2c = 1
if type == "Add" then
scaler2 = 1*value
scaler2b = 1*value2
scaler2c = 1*value3
elseif type == "Divide" then
scaler2 = 1/value
scaler2b = 1/value2
scaler2c = 1/value3
end
if ModeOfGlitch == 666666 then
coroutine.resume(coroutine.create(function()
while true do
swait()
rng.Material = "Glass"
end
end))
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
scaler2b = scaler2b - 0.01*value/bonuspeed
scaler2c = scaler2c - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
scaler2b = scaler2b - 0.01/value*bonuspeed
scaler2c = scaler2c - 0.01/value*bonuspeed
end
rng.Transparency = rng.Transparency + 0.01*bonuspeed
rngm.Scale = rngm.Scale + Vector3.new(scaler2*bonuspeed, scaler2b*bonuspeed, scaler2c*bonuspeed)
end
rng:Destroy()
end))
end

function ragdoll(who)
	pcall(function()
	local ded = who
	local h1 = ded:FindFirstChild("Head")
	local t1 = ded:FindFirstChild("Torso") or ded:FindFirstChild("UpperTorso")
	if t1.Name == "UpperTorso" then
		r15r(ded)
		return ragdoll
	end
	local ff = ded:FindFirstChildOfClass("ForceField")
	if ff ~= nil then
		ff:Destroy()
	end
	local ra1 = ded:FindFirstChild("Right Arm")
	local la1 = ded:FindFirstChild("Left Arm")
	local rl1 = ded:FindFirstChild("Right Leg")
	local ll1 = ded:FindFirstChild("Left Leg")
	local hum1 = ded:FindFirstChildOfClass("Humanoid")
	local acc = ded:FindFirstChild("xdded")
	if acc == nil then 
	local ac = Instance.new("Glue",ded) ac.Name = "xdded"
	local rut1 = ded:FindFirstChild("HumanoidRootPart")
	hum1.PlatformStand = true
	if rut1 ~= nil then
		rut1.Parent = nil
	end
	pcall(function()
	for i,v in pairs(who:GetChildren()) do
		if v:IsA("Accessory") or v:IsA("Hat") then
			local gl = Instance.new("WeldConstraint",v.Handle)
			gl.Part0 = h1 gl.Part1 = v.Handle
		end
	end
	for i,v in pairs(who:GetChildren()) do
		if v:IsA("Script") or v:IsA("LocalScript") then
			if v.Name == "Health" then
				v.Parent = nil
			end
		end
	end
	for i,v in pairs(who:GetChildren()) do
		if v:IsA("BasePart") then
			v.Anchored = false
		end
		if v:IsA("Accessory") or v:IsA("Hat") then
			v.Handle.Anchored = false
		end
	end
	end)
	hum1.MaxHealth = 100
	hum1.Health = math.random(10,50)
	t1:BreakJoints()
	ra1:BreakJoints()
	la1:BreakJoints()
	rl1:BreakJoints()
	ll1:BreakJoints()
	--//Joints~//--
	--Neck--
	if h1 ~= nil then
	local neca = Instance.new("Glue",t1) neca.Part0 = t1 neca.Part1 = h1 neca.C0 = CFrame.new(0,t1.Size.y/2,0) neca.C1 = CFrame.new(0,-h1.Size.y/2,0) neca.Name = "yo"
	local ch = Instance.new("Part",t1) ch.Name = "yo" ch.Size = Vector3.new(h1.Size.x/2,h1.Size.y/2,h1.Size.z/2 + h1.Size.z/4) ch.TopSurface = "Smooth" ch.BottomSurface = "Smooth" ch.Transparency = 1
	local wh = Instance.new("Weld",ch) wh.Part0 = h1 wh.Part1 = ch
	end
	--Right Arm--
	if ra1 ~= nil then
	local rsa = Instance.new("Glue",t1) rsa.Part0 = t1 rsa.Part1 = ra1 rsa.C0 = CFrame.new(t1.Size.x/2 + t1.Size.x/4,t1.Size.y/4,0) rsa.C1 = CFrame.new(0,ra1.Size.y/4,0) rsa.Name = "yo"
	local cra = Instance.new("Part",t1) cra.Name = "yo" cra.Size = Vector3.new(ra1.Size.y/2 + ra1.Size.y/4,ra1.Size.y/2,ra1.Size.z) cra.TopSurface = "Smooth" cra.BottomSurface = "Smooth" cra.Transparency = 1
	local wra = Instance.new("Weld",cra) wra.Part0 = ra1 wra.Part1 = cra wra.C0 = CFrame.new(0,-ra1.Size.y/8,0) * CFrame.Angles(rad(0),rad(0),rad(90))
	end
	--Left Arm--
	if la1 ~= nil then
	local lsa = Instance.new("Glue",t1) lsa.Part0 = t1 lsa.Part1 = la1 lsa.C0 = CFrame.new(-t1.Size.x/2 - t1.Size.x/4,t1.Size.y/4,0) lsa.C1 = CFrame.new(0,la1.Size.y/4,0) lsa.Name = "yo"
	local cla = Instance.new("Part",t1) cla.Name = "yo" cla.Size = Vector3.new(la1.Size.y/2 + la1.Size.y/4,la1.Size.y/2,la1.Size.z) cla.TopSurface = "Smooth" cla.BottomSurface = "Smooth" cla.Transparency = 1
	local wla = Instance.new("Weld",cla) wla.Part0 = la1 wla.Part1 = cla wla.C0 = CFrame.new(0,-la1.Size.y/8,0) * CFrame.Angles(rad(0),rad(0),rad(90))
	end
	--Right Leg--
	if rl1 ~= nil then
	local rha = Instance.new("Glue",t1) rha.Part0 = t1 rha.Part1 = rl1 rha.C0 = CFrame.new(t1.Size.x/4,-t1.Size.y/2,0) rha.C1 = CFrame.new(0,rl1.Size.y/2,0) rha.Name = "yo"
	local crl = Instance.new("Part",t1) crl.Name = "yo" crl.Size = Vector3.new(rl1.Size.y/2 + rl1.Size.y/4,rl1.Size.y/2,rl1.Size.z) crl.TopSurface = "Smooth" crl.BottomSurface = "Smooth" crl.Transparency = 1
	local wrl = Instance.new("Weld",crl) wrl.Part0 = rl1 wrl.Part1 = crl wrl.C0 = CFrame.new(0,-rl1.Size.y/8,0) * CFrame.Angles(rad(0),rad(0),rad(90))
	end
	--Left Leg--
	if ll1 ~= nil then
	local lha = Instance.new("Glue",t1) lha.Part0 = t1 lha.Part1 = ll1 lha.C0 = CFrame.new(-t1.Size.x/4,-t1.Size.y/2,0) lha.C1 = CFrame.new(0,ll1.Size.y/2,0) lha.Name = "yo"
	local cll = Instance.new("Part",t1) cll.Name = "yo" cll.Size = Vector3.new(ll1.Size.y/2 + ll1.Size.y/4,ll1.Size.y/2,ll1.Size.z) cll.TopSurface = "Smooth" cll.BottomSurface = "Smooth" cll.Transparency = 1
	local wll = Instance.new("Weld",cll) wll.Part0 = ll1 wll.Part1 = cll wll.C0 = CFrame.new(0,-ll1.Size.y/8,0) * CFrame.Angles(rad(0),rad(0),rad(90))
	end
	--//End of Joints~//--
end
end)
end
function r15r(who)
	local ded = who
	local ac = ded:FindFirstChild("xdded")
	if ac == nil then
	Instance.new("Glue",ded).Name = "xdded"
	local h2 = ded:FindFirstChild("Head")
	local t2 = ded:FindFirstChild("UpperTorso")
	local lt2 = ded:FindFirstChild("LowerTorso")
	local rua2 = ded:FindFirstChild("RightUpperArm")
	local lua2 = ded:FindFirstChild("LeftUpperArm")
	local rla2 = ded:FindFirstChild("RightLowerArm")
	local lla2 = ded:FindFirstChild("LeftLowerArm")
	local rh2 = ded:FindFirstChild("RightHand")
	local lh2 = ded:FindFirstChild("LeftHand")
	local rul2 = ded:FindFirstChild("RightUpperLeg")
	local lul2 = ded:FindFirstChild("LeftUpperLeg")
	local rll2 = ded:FindFirstChild("RightLowerLeg")
	local lll2 = ded:FindFirstChild("LeftLowerLeg")
	local rf2 = ded:FindFirstChild("RightFoot")
	local lf2 = ded:FindFirstChild("LeftFoot")
	local rut1 = ded:FindFirstChild("HumanoidRootPart")
	if rut1 ~= nil then
		rut1:Destroy()
	end
	for i,v in pairs(who:GetChildren()) do
		if v:IsA("Script") or v:IsA("LocalScript") then
			if v.Name == "Health" then
				v.Parent = nil
			end
		end
	end
	local hum1 = ded:FindFirstChildOfClass("Humanoid")
	hum1.PlatformStand = true
	hum1.MaxHealth = 100
	hum1.Health = math.random(10,50)
	local trans = 1
	if h2 ~= nil then
		h2:BreakJoints()
		local nc = Instance.new("Glue",t2) nc.Name = "yo" nc.C0 = CFrame.new(0,t2.Size.y/2,0) nc.C1 = CFrame.new(0,-h2.Size.y/2,0) nc.Part0 = t2 nc.Part1 = h2
		local ncc = Instance.new("Part",t2) ncc.Name = "yo" ncc.Transparency = trans ncc.Size = Vector3.new(h2.Size.x/2 + h2.Size.x/4,h2.Size.y/2,h2.Size.z)
		local ncw = Instance.new("Weld",t2) ncw.Name = "yo" ncw.Part0 = h2 ncw.Part1 = ncc
	end
	for i,v in pairs(who:GetChildren()) do
		if v:IsA("BasePart") then
			v.Anchored = false
		end
		if v:IsA("Accessory") or v:IsA("Hat") then
			v.Handle.Anchored = false
			local aw = Instance.new("WeldConstraint",v) aw.Part0 = h2 aw.Part1 = v.Handle
		end
	end
	hum1.PlatformStand = true
	if lt2 ~= nil then
		lt2:BreakJoints()
		local tc = Instance.new("Glue",t2) tc.Name = "yo" tc.C0 = CFrame.new(0,-t2.Size.y/2,0) tc.C1 = CFrame.new(0,lt2.Size.y/2,0) tc.Part0 = t2 tc.Part1 = lt2
		local tcc = Instance.new("Part",t2) tcc.Name = "yo" tcc.Transparency = trans tcc.Size = Vector3.new(lt2.Size.x/2,lt2.Size.y/2,lt2.Size.z)
		local tcw = Instance.new("Weld",t2) tcw.Name = "yo" tcw.Part0 = lt2 tcw.Part1 = tcc
	end
	if rua2 ~= nil then
		rua2:BreakJoints()
		local ruac = Instance.new("Glue",t2) ruac.Name = "yo" ruac.C0 = CFrame.new(t2.Size.x/2 + t2.Size.y/4,t2.Size.y/4,0) ruac.C1 = CFrame.new(0,rua2.Size.y/4.6666,0) ruac.Part0 = t2 ruac.Part1 = rua2
		local ruacc = Instance.new("Part",t2) ruacc.Name = "yo" ruacc.Transparency = trans ruacc.Size = Vector3.new(rua2.Size.x/1.2,rua2.Size.y/4,rua2.Size.z/1.2)
		local ruacw = Instance.new("Weld",t2) ruacw.Name = "yo" ruacw.Part0 = rua2 ruacw.Part1 = ruacc ruacw.C0 = CFrame.new(0,rua2.Size.y/7,0)
	end
	if lua2 ~= nil then
		lua2:BreakJoints()
		local luac = Instance.new("Glue",t2) luac.Name = "yo" luac.C0 = CFrame.new(-t2.Size.x/2 + -t2.Size.y/4,t2.Size.y/4,0) luac.C1 = CFrame.new(0,lua2.Size.y/4.6666,0) luac.Part0 = t2 luac.Part1 = lua2
		local luacc = Instance.new("Part",t2) luacc.Name = "yo" luacc.Transparency = trans luacc.Size = Vector3.new(lua2.Size.x/1.2,lua2.Size.y/4,lua2.Size.z/1.2)
		local luacw = Instance.new("Weld",t2) luacw.Name = "yo" luacw.Part0 = lua2 luacw.Part1 = luacc luacw.C0 = CFrame.new(0,lua2.Size.y/7,0)
	end
	if rla2 ~= nil then
		rla2:BreakJoints()
		local rlac = Instance.new("Glue",t2) rlac.Name = "yo" rlac.C0 = CFrame.new(0,-rua2.Size.y/4.6666,0) rlac.C1 = CFrame.new(0,rla2.Size.y/8,0) rlac.Part0 = rua2 rlac.Part1 = rla2
		local rlacc = Instance.new("Part",t2) rlacc.Name = "yo" rlacc.Transparency = trans rlacc.Size = Vector3.new(rla2.Size.x/1.2,rla2.Size.y/4,rla2.Size.z/1.2)
		local rlacw = Instance.new("Weld",t2) rlacw.Name = "yo" rlacw.Part0 = rla2 rlacw.Part1 = rlacc rlacw.C0 = CFrame.new(0,-rla2.Size.y/7,0)
	end
	if lla2 ~= nil then
		lla2:BreakJoints()
		local llac = Instance.new("Glue",t2) llac.Name = "yo" llac.C0 = CFrame.new(0,-lua2.Size.y/4.6666,0) llac.C1 = CFrame.new(0,lla2.Size.y/8,0) llac.Part0 = lua2 llac.Part1 = lla2
		local llacc = Instance.new("Part",t2) llacc.Name = "yo" llacc.Transparency = trans llacc.Size = Vector3.new(lla2.Size.x/1.2,lla2.Size.y/4,lla2.Size.z/1.2)
		local llacw = Instance.new("Weld",t2) llacw.Name = "yo" llacw.Part0 = lla2 llacw.Part1 = llacc llacw.C0 = CFrame.new(0,-lla2.Size.y/7,0)
	end
	if rh2 ~= nil then
		rh2:BreakJoints()
		local rhc = Instance.new("Glue",t2) rhc.Name = "yo" rhc.C0 = CFrame.new(0,-rla2.Size.y/2,0) rhc.C1 = CFrame.new(0,rh2.Size.y/3,0) rhc.Part0 = rla2 rhc.Part1 = rh2
		local rhcc = Instance.new("Part",t2) rhcc.Name = "yo" rhcc.Transparency = trans rhcc.Size = Vector3.new(rh2.Size.x/1.2,rh2.Size.y,rh2.Size.z/1.2)
		local rhcw = Instance.new("Weld",t2) rhcw.Name = "yo" rhcw.Part0 = rh2 rhcw.Part1 = rhcc rhcw.C0 = CFrame.new(0,0,0)
	end
	if lh2 ~= nil then
		lh2:BreakJoints()
		local lhc = Instance.new("Glue",t2) lhc.Name = "yo" lhc.C0 = CFrame.new(0,-lla2.Size.y/2,0) lhc.C1 = CFrame.new(0,lh2.Size.y/3,0) lhc.Part0 = lla2 lhc.Part1 = lh2
		local lhcc = Instance.new("Part",t2) lhcc.Name = "yo" lhcc.Transparency = trans lhcc.Size = Vector3.new(lh2.Size.x/1.2,lh2.Size.y,lh2.Size.z/1.2)
		local lhcw = Instance.new("Weld",t2) lhcw.Name = "yo" lhcw.Part0 = lh2 lhcw.Part1 = lhcc lhcw.C0 = CFrame.new(0,0,0)
	end
	if rul2 ~= nil then
		rul2:BreakJoints()
		local rulc = Instance.new("Glue",t2) rulc.Name = "yo" rulc.C0 = CFrame.new(lt2.Size.x/4,-lt2.Size.y/2,0) rulc.C1 = CFrame.new(0,rul2.Size.y/3,0) rulc.Part0 = lt2 rulc.Part1 = rul2
		local rulcc = Instance.new("Part",t2) rulcc.Name = "yo" rulcc.Transparency = trans rulcc.Size = Vector3.new(rul2.Size.x/1.2,rul2.Size.y/4,rul2.Size.z/1.2)
		local rulcw = Instance.new("Weld",t2) rulcw.Name = "yo" rulcw.Part0 = rul2 rulcw.Part1 = rulcc rulcw.C0 = CFrame.new(0,rul2.Size.y/7,0)
	end
	if lul2 ~= nil then
		lul2:BreakJoints()
		local lulc = Instance.new("Glue",t2) lulc.Name = "yo" lulc.C0 = CFrame.new(-lt2.Size.x/4,-lt2.Size.y/2,0) lulc.C1 = CFrame.new(0,lul2.Size.y/3,0) lulc.Part0 = lt2 lulc.Part1 = lul2
		local lulcc = Instance.new("Part",t2) lulcc.Name = "yo" lulcc.Transparency = trans lulcc.Size = Vector3.new(lul2.Size.x/1.2,lul2.Size.y/4,lul2.Size.z/1.2)
		local lulcw = Instance.new("Weld",t2) lulcw.Name = "yo" lulcw.Part0 = lul2 lulcw.Part1 = lulcc lulcw.C0 = CFrame.new(0,lul2.Size.y/7,0)
	end
	if rll2 ~= nil then
		rll2:BreakJoints()
		local rllc = Instance.new("Glue",t2) rllc.Name = "yo" rllc.C0 = CFrame.new(0,-rll2.Size.y/6,0) rllc.C1 = CFrame.new(0,rll2.Size.y/5,0) rllc.Part0 = rul2 rllc.Part1 = rll2
		local rllcc = Instance.new("Part",t2) rllcc.Name = "yo" rllcc.Transparency = trans rllcc.Size = Vector3.new(rll2.Size.x/1.2,rll2.Size.y/3.5,rll2.Size.z/1.2)
		local rllcw = Instance.new("Weld",t2) rllcw.Name = "yo" rllcw.Part0 = rll2 rllcw.Part1 = rllcc rllcw.C0 = CFrame.new(0,-rll2.Size.y/7,0)
	end
	if lll2 ~= nil then
		lll2:BreakJoints()
		local lllc = Instance.new("Glue",t2) lllc.Name = "yo" lllc.C0 = CFrame.new(0,-lll2.Size.y/6,0) lllc.C1 = CFrame.new(0,lll2.Size.y/5,0) lllc.Part0 = lul2 lllc.Part1 = lll2
		local lllcc = Instance.new("Part",t2) lllcc.Name = "yo" lllcc.Transparency = trans lllcc.Size = Vector3.new(lll2.Size.x/1.2,lll2.Size.y/3.5,lll2.Size.z/1.2)
		local lllcw = Instance.new("Weld",t2) lllcw.Name = "yo" lllcw.Part0 = lll2 lllcw.Part1 = lllcc lllcw.C0 = CFrame.new(0,-lll2.Size.y/7,0)
	end
	if rf2 ~= nil then
		rf2:BreakJoints()
		local rfc = Instance.new("Glue",t2) rfc.Name = "yo" rfc.C0 = CFrame.new(0,-rul2.Size.y/2.7,0) rfc.C1 = CFrame.new(0,rll2.Size.y/6,0) rfc.Part0 = rll2 rfc.Part1 = rf2
		local rfcc = Instance.new("Part",t2) rfcc.Name = "yo" rfcc.Transparency = trans rfcc.Size = Vector3.new(rf2.Size.x/1,rf2.Size.y,rf2.Size.z/1)
		local rfcw = Instance.new("Weld",t2) rfcw.Name = "yo" rfcw.Part0 = rf2 rfcw.Part1 = rfcc
	end
	if lf2 ~= nil then
		lf2:BreakJoints()
		local lfc = Instance.new("Glue",t2) lfc.Name = "yo" lfc.C0 = CFrame.new(0,-lul2.Size.y/2.7,0) lfc.C1 = CFrame.new(0,lll2.Size.y/6,0) lfc.Part0 = lll2 lfc.Part1 = lf2
		local lfcc = Instance.new("Part",t2) lfcc.Name = "yo" lfcc.Transparency = trans lfcc.Size = Vector3.new(lf2.Size.x/1,lf2.Size.y,lf2.Size.z/1)
		local lfcw = Instance.new("Weld",t2) lfcw.Name = "yo" lfcw.Part0 = lf2 lfcw.Part1 = lfcc
	end
	end
end

function slash(bonuspeed,rotspeed,rotatingop,typeofshape,type,typeoftrans,pos,scale,value,color)
local type = type
local rotenable = rotatingop
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
if typeoftrans == "In" then
rng.Transparency = 1
end
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "FileMesh"
if typeofshape == "Normal" then
rngm.MeshId = "rbxassetid://662586858"
elseif typeofshape == "Round" then
rngm.MeshId = "rbxassetid://662585058"
end
rngm.Scale = scale
local scaler2 = 1/10
if type == "Add" then
scaler2 = 1*value/10
elseif type == "Divide" then
scaler2 = 1/value/10
end
local randomrot = math.random(1,2)
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed/10
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed/10
end
if rotenable == true then
if randomrot == 1 then
rng.CFrame = rng.CFrame*CFrame.Angles(0,math.rad(rotspeed*bonuspeed/2),0)
elseif randomrot == 2 then
rng.CFrame = rng.CFrame*CFrame.Angles(0,math.rad(-rotspeed*bonuspeed/2),0)
end
end
if typeoftrans == "Out" then
rng.Transparency = rng.Transparency + 0.01*bonuspeed
elseif typeoftrans == "In" then
rng.Transparency = rng.Transparency - 0.01*bonuspeed
end
rngm.Scale = rngm.Scale + Vector3.new(scaler2*bonuspeed/10, 0, scaler2*bonuspeed/10)
end
rng:Destroy()
end))
end

function PixelBlock(bonuspeed,FastSpeed,type,pos,x1,y1,z1,value,color,outerpos)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*outerpos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Brick"
rngm.Scale = vt(x1,y1,z1)
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
local scaler2 = 1
local speeder = FastSpeed/10
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
end
if chaosmode == true then
rng.BrickColor = BrickColor.random()
end
speeder = speeder - 0.01*FastSpeed*bonuspeed/10
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*speeder*bonuspeed
--rng.Transparency = rng.Transparency + 0.01*bonuspeed
rngm.Scale = rngm.Scale - Vector3.new(scaler2*bonuspeed, scaler2*bonuspeed, scaler2*bonuspeed)
end
rng:Destroy()
end))
end

function PixelBlockX(bonuspeed,FastSpeed,type,pos,x1,y1,z1,value,color,outerpos)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
if ModeOfGlitch ~= 666666 then
        rng.Material = "Neon"
elseif ModeOfGlitch == 666666 then
	rng.Material = "Glass"
	end
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*outerpos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Brick"
rngm.Scale = vt(x1,y1,z1)
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
local scaler2 = 1
local speeder = FastSpeed/10
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
end
if chaosmode == true then
rng.BrickColor = BrickColor.random()
end
speeder = speeder - 0.01*FastSpeed*bonuspeed/10
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*speeder*bonuspeed
rng.Transparency = rng.Transparency + 0.01*bonuspeed
rngm.Scale = rngm.Scale - Vector3.new(scaler2*bonuspeed, scaler2*bonuspeed, scaler2*bonuspeed)
end
rng:Destroy()
end))
end

function PixelBlockNeg(bonuspeed,FastSpeed,type,pos,x1,y1,z1,value,color,outerpos)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*outerpos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Brick"
rngm.Scale = vt(x1,y1,z1)
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
local scaler2 = 0
local speeder = FastSpeed/10
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
end
if chaosmode == true then
rng.BrickColor = BrickColor.random()
end
speeder = speeder + 0.01*FastSpeed*bonuspeed/10
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*speeder*bonuspeed
--rng.Transparency = rng.Transparency + 0.01*bonuspeed
rngm.Scale = rngm.Scale - Vector3.new(scaler2*bonuspeed, scaler2*bonuspeed, scaler2*bonuspeed)
end
rng:Destroy()
end))
end

function block(bonuspeed,type,pos,scale,value,value2,value3,color,color3)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.Color = color3
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Brick"
rngm.Scale = scale
local scaler2 = 1
local scaler2b = 1
local scaler2c = 1
if type == "Add" then
scaler2 = 1*value
scaler2b = 1*value2
scaler2c = 1*value3
elseif type == "Divide" then
scaler2 = 1/value
scaler2b = 1/value2
scaler2c = 1/value3
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
scaler2b = scaler2b - 0.01*value/bonuspeed
scaler2c = scaler2c - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
scaler2b = scaler2b - 0.01/value*bonuspeed
scaler2c = scaler2c - 0.01/value*bonuspeed
end
rng.CFrame = rng.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
rng.Transparency = rng.Transparency + 0.01*bonuspeed
rngm.Scale = rngm.Scale + Vector3.new(scaler2*bonuspeed, scaler2b*bonuspeed, scaler2c*bonuspeed)
end
rng:Destroy()
end))
end

function sphereMK(bonuspeed,FastSpeed,type,pos,x1,y1,z1,value,color,outerpos)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 0
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*outerpos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Sphere"
rngm.Scale = vt(x1,y1,z1)
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
local scaler2 = 1
local speeder = FastSpeed
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
end
if chaosmode == true then
rng.BrickColor = BrickColor.random()
end
speeder = speeder - 0.01*FastSpeed*bonuspeed
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*speeder*bonuspeed
rng.Transparency = rng.Transparency + 0.01*bonuspeed
rngm.Scale = rngm.Scale + Vector3.new(scaler2*bonuspeed, scaler2*bonuspeed, 0)
end
rng:Destroy()
end))
end


function sphereMKCharge(bonuspeed,FastSpeed,type,pos,x1,y1,z1,value,color,outerpos)
local type = type
local rng = Instance.new("Part", char)
        rng.Anchored = true
        rng.BrickColor = color
        rng.CanCollide = false
        rng.FormFactor = 3
        rng.Name = "Ring"
        rng.Material = "Neon"
        rng.Size = Vector3.new(1, 1, 1)
        rng.Transparency = 1
        rng.TopSurface = 0
        rng.BottomSurface = 0
        rng.CFrame = pos
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*outerpos
        local rngm = Instance.new("SpecialMesh", rng)
        rngm.MeshType = "Sphere"
rngm.Scale = vt(x1,y1,z1)
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end

local scaler2 = 1
local speeder = FastSpeed
if type == "Add" then
scaler2 = 1*value
elseif type == "Divide" then
scaler2 = 1/value
end
coroutine.resume(coroutine.create(function()
for i = 0,10/bonuspeed,0.1 do
swait()
if rainbowmode == true then
rng.Color = Color3.new(r/255,g/255,b/255)
end
if type == "Add" then
scaler2 = scaler2 - 0.01*value/bonuspeed
elseif type == "Divide" then
scaler2 = scaler2 - 0.01/value*bonuspeed
end
if chaosmode == true then
rng.BrickColor = BrickColor.random()
end
speeder = speeder - 0.01*FastSpeed*bonuspeed
rng.CFrame = rng.CFrame + rng.CFrame.lookVector*speeder*bonuspeed
rng.Transparency = rng.Transparency - 0.01*bonuspeed
rngm.Scale = rngm.Scale + Vector3.new(scaler2*bonuspeed, scaler2*bonuspeed, 0)
end
rng:Destroy()
end))
end

function dmg(dude)
if dude.Name ~= Character then
Flingy(dude)
end
end

function FindNearestHead(Position, Distance, SinglePlayer)
	if SinglePlayer then
		return (SinglePlayer.Torso.CFrame.p - Position).magnitude < Distance
	end
	local List = {}
	for i, v in pairs(workspace:GetChildren()) do
		if v:IsA("Model") then
			if v:findFirstChild("Head") then
				if v ~= Character then
					if (v.Head.Position - Position).magnitude <= Distance then
						table.insert(List, v)
					end 
				end 
			end 
		end 
	end
	return List
end

function FaceMouse()
  Cam = workspace.CurrentCamera
  return {
    CFrame.new(char.Torso.Position, Vector3.new(mouse.Hit.p.x, char.Torso.Position.y, mouse.Hit.p.z)),
    Vector3.new(mouse.Hit.p.x, mouse.Hit.p.y, mouse.Hit.p.z)
  }
end

function FaceMouse2()
  Cam = workspace.CurrentCamera
  return {
    CFrame.new(char.Torso.Position, Vector3.new(mouse.Hit.p.x, mouse.Hit.p.y, mouse.Hit.p.z)),
    Vector3.new(mouse.Hit.p.x, mouse.Hit.p.y, mouse.Hit.p.z)
  }
end


-- Functions are ready.
local storehumanoidWS = 16

function createBGCircle(size,parent,color)
local bgui = Instance.new("BillboardGui",parent)
bgui.Size = UDim2.new(size, 0, size, 0)
local imgc = Instance.new("ImageLabel",bgui)
imgc.BackgroundTransparency = 1
imgc.ImageTransparency = 0
imgc.Size = UDim2.new(1,0,1,0)
imgc.Image = "rbxassetid://997291547" --997291547,521073910
imgc.ImageColor3 = color
return bgui,imgc
end

function symbolizeBlink(guipar,size,img,color,bonussize,vol,pit,soundid,spar,rotationenabled,rotsp,delay)
local bgui,imgc = createBGCircle(size,guipar,color)
bgui.AlwaysOnTop = true
imgc.Image = "rbxassetid://" ..img
local rrot = math.random(1,2)
CFuncs["Sound"].Create("rbxassetid://" ..soundid, spar, vol,pit)
coroutine.resume(coroutine.create(function()
for i = 0, 24*delay do
swait()
if rotationenabled == true then
if rrot == 1 then
imgc.Rotation = imgc.Rotation + rotsp
elseif rrot == 2 then
imgc.Rotation = imgc.Rotation - rotsp
end
end
bgui.Size = bgui.Size + UDim2.new(1*bonussize/delay,0,1*bonussize/delay,0)
imgc.ImageTransparency = imgc.ImageTransparency + 0.04/delay
end
bgui:Destroy()
end))
end
function RecolorThing(one,two,three,four,five,exonetran,exone,extwotran,extwo,secondaryenabled,sectrailenabled)
for i, v in pairs(mw2:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = one
v.Material = "Neon"
end
end
CFuncs["EchoSound"].Create("rbxassetid://847061203", root, 1, 1,0,10,0.25,0.25,1)
symbolizeBlink(root,0,2092248396,one.Color,5,3,1,847061203,root,true,10,1)
symbolizeBlink(root,0,2092248396,one.Color,4,0,0,0,root,true,-5,1)
tr1.Color = ColorSequence.new(one.Color)
tr2.Color = ColorSequence.new(one.Color)
tr3.Color = ColorSequence.new(one.Color)
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
if secondaryenabled == false then
v.Transparency = 1
elseif secondaryenabled == true then
v.Transparency = 0
end
v.BrickColor = two
v.Material = "Neon"
end
end
if secondaryenabled == false then
tl1.Enabled = false
tl2.Enabled = false
tl3.Enabled = false
elseif secondaryenabled == true then
tl1.Enabled = true
tl2.Enabled = true
tl3.Enabled = true
end
refec.Color = ColorSequence.new(MAINRUINCOLOR.Color)
tl1.Color = ColorSequence.new(two.Color)
tl2.Color = ColorSequence.new(two.Color)
tl3.Color = ColorSequence.new(two.Color)
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = three
v.Material = "Ice"
end
end
for i, v in pairs(m2:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = four
v.Material = "Ice"
end
end
for i, v in pairs(m3:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = five
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = exonetran
v.BrickColor = exone
v.Material = "Neon"
end
end
if sectrailenabled == true then
tl4.Enabled = false
tl5.Enabled = false
tl6.Enabled = false
tr4.Enabled = false
tr5.Enabled = false
tr6.Enabled = false
tl4.Color = ColorSequence.new(exone.Color)
tl5.Color = ColorSequence.new(exone.Color)
tl6.Color = ColorSequence.new(exone.Color)
tr4.Color = ColorSequence.new(extwo.Color)
tr5.Color = ColorSequence.new(extwo.Color)
tr6.Color = ColorSequence.new(extwo.Color)
elseif sectrailenabled == false then
tl4.Enabled = false
tl5.Enabled = false
tl6.Enabled = false
tr4.Enabled = false
tr5.Enabled = false
tr6.Enabled = false
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = extwotran
v.BrickColor = extwo
v.Material = "Neon"
end
end
end

function RecolorThing2(one,two,three,four,five,exonetran,exone,extwotran,extwo,secondaryenabled,sectrailenabled)
local keptcolor = one
local locat = Instance.new("Part", char)
locat.CanCollide = false
locat.FormFactor = 3
locat.Name = "Ring"
locat.Material = "Neon"
locat.Size = Vector3.new(1, 1, 1)
locat.Transparency = 1
locat.TopSurface = 0
locat.BottomSurface = 0
locat.Anchored = true
locat.CFrame = root.CFrame*CFrame.new(0,-3,0)
local poste = 0
local rotation = 0
local upperpos = 0
local rate = 0
local x = locat

for i, v in pairs(mw2:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = one
v.Material = "Neon"
end
end

tbeam(one,two)
tr1.Color = ColorSequence.new(one.Color)
tr2.Color = ColorSequence.new(one.Color)
tr3.Color = ColorSequence.new(one.Color)
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
if secondaryenabled == false then
v.Transparency = 1
elseif secondaryenabled == true then
v.Transparency = 1
end
v.BrickColor = two
v.Material = "Neon"
end
end
if secondaryenabled == false then
tl1.Enabled = false
tl2.Enabled = false
tl3.Enabled = false
elseif secondaryenabled == true then
tl1.Enabled = false
tl2.Enabled = false
tl3.Enabled = false
end
refec.Color = ColorSequence.new(MAINRUINCOLOR.Color)
tl1.Color = ColorSequence.new(two.Color)
tl2.Color = ColorSequence.new(two.Color)
tl3.Color = ColorSequence.new(two.Color)
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = three
v.Material = "Ice"
end
end
for i, v in pairs(m2:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = four
v.Material = "Ice"
end
end
for i, v in pairs(m3:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = five
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = exonetran
v.BrickColor = exone
v.Material = "Neon"
end
end
if sectrailenabled == true then
tl4.Enabled = true
tl5.Enabled = true
tl6.Enabled = true
tr4.Enabled = true
tr5.Enabled = true
tr6.Enabled = true
tl4.Color = ColorSequence.new(exone.Color)
tl5.Color = ColorSequence.new(exone.Color)
tl6.Color = ColorSequence.new(exone.Color)
tr4.Color = ColorSequence.new(extwo.Color)
tr5.Color = ColorSequence.new(extwo.Color)
tr6.Color = ColorSequence.new(extwo.Color)
elseif sectrailenabled == false then
tl4.Enabled = false
tl5.Enabled = false
tl6.Enabled = false
tr4.Enabled = false
tr5.Enabled = false
tr6.Enabled = false
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = extwotran
v.BrickColor = extwo
v.Material = "Neon"
end
end
end

function normalmog()
attack = true
hum.WalkSpeed = 0
CFuncs["Sound"].Create("rbxassetid://136007472", root, 5, 1.25)
for i = 0,6,0.1 do
swait()
sphereMK(2.5,-1.5,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),3.5,3.5,45,-0.035,MAINRUINCOLOR,100)
slash(math.random(30,60)/10,5,true,"Round","Add","In",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.5,0.01,0.5),-0.5,MAINRUINCOLOR)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.2)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.3,-0.15)*angles(math.rad(30),math.rad(0),math.rad(0)),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(30),math.rad(0),math.rad(0 - 5 * math.cos(sine / 0.2))),.2)
RW.C0=clerp(RW.C0,cf(1.05,0.4,-0.5)*angles(math.rad(140),math.rad(0),math.rad(-50)),.2)
LW.C0=clerp(LW.C0,cf(-1.05,0.4,-0.5)*angles(math.rad(140),math.rad(0),math.rad(50)),.2)
end
CFuncs["Sound"].Create("rbxassetid://206082327", root, 7.5,1)
CFuncs["Sound"].Create("rbxassetid://847061203", root, 10,1)
CFuncs["Sound"].Create("rbxassetid://239000203", root, 7.5,1)
CFuncs["Sound"].Create("rbxassetid://579687077", root, 7.5,0.75)
CFuncs["Sound"].Create("rbxassetid://1368637781", root, 10,1)
CFuncs["Sound"].Create("rbxassetid://763718160", root, 7.5, 1.1)
CFuncs["Sound"].Create("rbxassetid://782353443", root, 7.5, 1)
rainbowmode = false
chaosmode = false
ModeOfGlitch = 1
storehumanoidWS = 16
newTheme("rbxassetid://415898123",48.6,1,1.25)
RecolorTextAndRename("MAYHEM",Color3.new(0.25,0,0),Color3.new(1,0,0),"Antique")
MAINRUINCOLOR = BrickColor.new("Really red")
sphere(2.5,"Add",root.CFrame,vt(0,0,0),1,MAINRUINCOLOR)
for i = 0, 49 do
PixelBlock(1,math.random(1,20),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2,2,2,0.04,MAINRUINCOLOR,0)
end
for i = 0, 24 do
sphere2(2,"Add",tors.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,7,-0.01,MAINRUINCOLOR)
slash(math.random(10,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(150,350)/250,BrickColor.new("White"))
end
for i = 0,3,0.1 do
sphereMK(2.5,-1,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2.5,2.5,25,-0.025,MAINRUINCOLOR,0)
end
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.5)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.5)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.3,-0.15)*angles(math.rad(-30),math.rad(0),math.rad(0)),.5)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-30),math.rad(0),math.rad(0 - 2.5 * math.cos(sine / 0.2))),.5)
RW.C0=clerp(RW.C0,cf(1.45,0.4,0)*angles(math.rad(-20),math.rad(0 - 2 * math.cos(sine / 0.2)),math.rad(80 + 2 * math.cos(sine / 0.2))),.5)
LW.C0=clerp(LW.C0,cf(-1.45,0.4,0)*angles(math.rad(-20),math.rad(0 + 2 * math.cos(sine / 0.2)),math.rad(-80 - 2 * math.cos(sine / 0.2))),.5)
end
hum.WalkSpeed = storehumanoidWS
attack = false
end

function attackone()
	attack = true
	CFuncs["Sound"].Create("rbxassetid://136007472", root, 2.5, 1.5)
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.2)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.3,-0.15)*angles(math.rad(30),math.rad(0),math.rad(0)),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(30),math.rad(0),math.rad(0 - 5 * math.cos(sine / 0.2))),.2)
RW.C0=clerp(RW.C0,cf(1.05,0.4,-0.5)*angles(math.rad(140),math.rad(0),math.rad(-50)),.2)
LW.C0=clerp(LW.C0,cf(-1.05,0.4,-0.5)*angles(math.rad(140),math.rad(0),math.rad(50)),.2)
end
for i, v in pairs(FindNearestHead(Torso.CFrame.p, 52.5)) do
if v:FindFirstChild('Torso') then
dmg(v)
end
end
CFuncs["Sound"].Create("rbxassetid://206082327", root, 7.5/2,1)
CFuncs["Sound"].Create("rbxassetid://847061203", root, 10/2,1)
CFuncs["Sound"].Create("rbxassetid://239000203", root, 7.5/2,1)
CFuncs["Sound"].Create("rbxassetid://579687077", root, 7.5/2,0.75)
CFuncs["Sound"].Create("rbxassetid://1368637781", root, 10/2,1)
CFuncs["Sound"].Create("rbxassetid://763718160", root, 7.5/2, 1.1)
CFuncs["Sound"].Create("rbxassetid://782353443", root, 7.5/2, 1)
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.5)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.5)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.3,-0.15)*angles(math.rad(-30),math.rad(0),math.rad(0)),.5)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-30),math.rad(0),math.rad(0 - 2.5 * math.cos(sine / 0.2))),.5)
RW.C0=clerp(RW.C0,cf(1.45,0.4,0)*angles(math.rad(-20),math.rad(0 - 2 * math.cos(sine / 0.2)),math.rad(80 + 2 * math.cos(sine / 0.2))),.5)
LW.C0=clerp(LW.C0,cf(-1.45,0.4,0)*angles(math.rad(-20),math.rad(0 + 2 * math.cos(sine / 0.2)),math.rad(-80 - 2 * math.cos(sine / 0.2))),.5)
end
	attack = false
end

function attacktwo()
	attack = true
hum.WalkSpeed = 2
local keptcolor = MAINRUINCOLOR
CFuncs["Sound"].Create("rbxassetid://847061203", root, 2, 1)
sphere2(5,"Add",rarm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(1,1,1),0.1,0.1,0.1,keptcolor,keptcolor.Color)
sphere2(5,"Add",rarm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(1,1,1),0.2,0.2,0.2,keptcolor,keptcolor.Color)
for i = 0,1,0.1 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(60)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-30)),.3)
RW.C0=clerp(RW.C0,cf(1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(-60)),.3)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(-10)),.3)
end
for i = 0, 1 do
CFuncs["Sound"].Create("rbxassetid://763755889", root, 3,1.1)
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-30)),.6)
RW.C0=clerp(RW.C0,cf(1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(-60)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(-10)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(-90)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-30)),.6)
RW.C0=clerp(RW.C0,cf(1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(-60)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(-10)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(-180)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-30)),.6)
RW.C0=clerp(RW.C0,cf(1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(-60)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(-10)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(-270)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-30)),.6)
RW.C0=clerp(RW.C0,cf(1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(-60)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(-10)),.6)
end
local rot = 0
local dis = CreateParta(char,0.5,1,"Neon",keptcolor)
CFuncs["EchoSound"].Create("rbxassetid://763718160", dis, 3, 1.1,0,10,0.15,0.5,1)
dis.CFrame = root.CFrame*CFrame.new(0,2,-3)
CreateMesh(dis,"Sphere",10,1,10)
local a = Instance.new("Part",char)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
dis.CFrame = dis.CFrame*CFrame.Angles(0,math.rad(rot),0)
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*250
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
local hitted = false
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
	CFuncs["EchoSound"].Create("rbxassetid://782200047", dis, 7, 1.1,0,10,0.15,0.5,1)
	MagniDamage(dis, 30, 33,56, 0, "Normal")
	sphere2(8,"Add",dis.CFrame,vt(10,1,10),1,0.1,1,keptcolor,keptcolor.Color)
	sphere2(4,"Add",dis.CFrame,vt(1,1,1),0.5,0.5,0.5,keptcolor,keptcolor.Color)
	sphere2(3,"Add",dis.CFrame,vt(1,1,1),0.5,0.5,0.5,keptcolor,keptcolor.Color)
	coroutine.resume(coroutine.create(function()
		for i = 0, 9 do
local disr = CreateParta(char,1,1,"Neon",keptcolor)
disr.CFrame = dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = disr.CFrame.lookVector*math.random(50,200)
bv.Parent = disr
local val = 0
coroutine.resume(coroutine.create(function()
	swait(30)
	for i = 0, 9 do
		swait()
		val = val + 0.1
		--trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, val),NumberSequenceKeypoint.new(1, 1)})
	end
game:GetService("Debris"):AddItem(disr, 3)
end))
end
end))
	for i = 0, 9 do
		--slash(math.random(10,20)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(100,200)/250,BrickColor.new("White"))
	end
for i = 0, 19 do
--PixelBlock(1,math.random(5,20),"Add",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),4,4,4,0.08,keptcolor,0)
end
coroutine.resume(coroutine.create(function()
for i = 0, 19 do
swait()
hum.CameraOffset = vt(math.random(-10,10)/70,math.random(-10,10)/70,math.random(-10,10)/70)
end
hum.CameraOffset = vt(0,0,0)
end))
dis.Anchored = true
dis.Transparency = 1
wait(8)
dis:Destroy()
end
end)
end))
rot = rot - 15
end
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(-30),math.rad(0)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(5)),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(-60)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(50)),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(90),math.rad(0),math.rad(60)),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(-10)),.3)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function attackthree()
attack = true
local keptcolor = MAINRUINCOLOR
CFuncs["Sound"].Create("rbxassetid://136007472", root, 2, 1.5)
	for i = 0,2,0.1 do
		swait()
		sphere2(5,"Add",larm.CFrame*CFrame.new(0,-1.5,0),vt(1,1,1),0.025,0.025,0.025,MAINRUINCOLOR,MAINRUINCOLOR.Color)
RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0,0,0)* angles(math.rad(10),math.rad(0),math.rad(50)),0.3)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(5),math.rad(0),math.rad(-50)),.3)
RW.C0 = clerp(RW.C0, CFrame.new(1.5, 0.5, 0) * angles(math.rad(80), math.rad(10), math.rad(60)), 0.3)
LW.C0 = clerp(LW.C0, CFrame.new(-1.5, 0.5, 0) * angles(math.rad(140), math.rad(0), math.rad(-70)), 0.3)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1.5),math.rad(-50),math.rad(-10)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(40)),.3)
	end
CFuncs["Sound"].Create("rbxassetid://763716870", root, 3,1)
CFuncs["Sound"].Create("rbxassetid://782353443", root, 5,0.9)
CFuncs["Sound"].Create("rbxassetid://782225570", root, 4,0.5)
CFuncs["Sound"].Create("rbxassetid://763717569", root, 3,1)
sphere2(5,"Add",root.CFrame,vt(1,1,1),1,1,1,MAINRUINCOLOR)
sphere2(5,"Add",root.CFrame,vt(1,1,1),0.5,0.5,0.5,MAINRUINCOLOR)
for i = 0, 24 do
		slash(math.random(10,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(90),math.rad(math.random(-360,360)),math.rad(0)),vt(0.01,0.01,0.01),math.random(50,200)/250,BrickColor.new("White"))
end
for i = 0,4,0.1 do
		swait()
		root.CFrame = root.CFrame + root.CFrame.lookVector*5
		local dis = CreateParta(char,0.25,1,"Neon",MAINRUINCOLOR)
CreateMesh(dis,"Sphere",1,1,1)
dis.Anchored = true
dis.CFrame = larm.CFrame*CFrame.new(0,-3,0)
sphere2(5,"Add",dis.CFrame,vt(1,1,1),0.1,0.1,0.1,dis.BrickColor,dis.Color)
coroutine.resume(coroutine.create(function()
	swait(30)
	dis.Transparency = 1
coroutine.resume(coroutine.create(function()
for i = 0, 19 do
swait()
hum.CameraOffset = vt(math.random(-10,10)/40,math.random(-10,10)/40,math.random(-10,10)/40)
end
hum.CameraOffset = vt(0,0,0)
end))
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(50,450)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.125)
eff.Enabled = false
end))
MagniDamage(dis, 30, 45,50, 0, "Normal")
    for i = 0, 2 do
		slash(math.random(10,80)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(50,150)/250,dis.BrickColor)
	end
	CFuncs["Sound"].Create("rbxassetid://782353117", dis, 1,1)
	CFuncs["Sound"].Create("rbxassetid://1666361078", dis, 1,1.5)
	CFuncs["Sound"].Create("rbxassetid://782353443", dis, 2,1.65)
	sphere2(3,"Add",dis.CFrame,vt(1,1,1),0.4,0.4,0.4,dis.BrickColor,dis.Color)
end))
game:GetService("Debris"):AddItem(dis, 5)
RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0,0,0)* angles(math.rad(89),math.rad(-8),math.rad(-5)),0.5)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(-30),math.rad(0),math.rad(8)),.5)
RW.C0 = clerp(RW.C0, CFrame.new(1.5, 0.5, 0) * angles(math.rad(-14), math.rad(1), math.rad(17)), 0.5)
LW.C0 = clerp(LW.C0, CFrame.new(-1.5, 0.85, 0) * angles(math.rad(180), math.rad(0), math.rad(-8)), 0.5)
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-8),math.rad(0),math.rad(-20)),.5)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(9),math.rad(0),math.rad(20)),.5)
	end
attack = false
end


local blush = Instance.new("Decal",hed)
blush.Texture = "rbxassetid://898404027"
blush.Face = "Front"
blush.Parent = nil
blush.Transparency = 1
----------------------------------- Abilities

function ExtinctiveHeartbreak()
local targetted = nil
if mouse.Target.Parent ~= Character and mouse.Target.Parent.Parent ~= Character and mouse.Target.Parent:FindFirstChildOfClass("Humanoid") ~= nil then
targetted = mouse.Target.Parent
end
if targetted ~= nil then
attack = true
CFuncs["Sound"].Create("rbxassetid://847061203", root, 2.5,1)
for i = 0, 9 do
sphereMK(3,0.25,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,10,-0.01,BrickColor.new("Really red"),0)
end
for i = 0, 24 do
PixelBlock(1,math.random(4,8),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),3,3,3,0.06,BrickColor.new("Really red"),0)
end
sphere(3,"Add",root.CFrame,vt(0,0,0),0.25,BrickColor.new("Really red"))
local originalpos = root.CFrame
RootPart.CFrame = targetted.Head.CFrame * CFrame.new(0,-2,2)
for i = 0, 9 do
sphereMK(3,0.25,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,10,-0.01,BrickColor.new("Really red"),0)
end
for i = 0, 24 do
PixelBlock(1,math.random(4,8),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),3,3,3,0.06,BrickColor.new("Really red"),0)
end
hum.WalkSpeed = 0
sphere(3,"Add",root.CFrame,vt(0,0,0),0.25,BrickColor.new("Really red"))
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("YOU WONT BE NECCESSARY.",MAINRUINCOLOR.Color,2)
elseif radm == 2 then
bosschatfunc("YOUR EXISTANCE WILL BE GONE.",MAINRUINCOLOR.Color,2)
elseif radm == 3 then
bosschatfunc("DIE!",MAINRUINCOLOR.Color,2)
end
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(-10),math.rad(0)),.4)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(0)),.4)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0),math.rad(0),math.rad(80)),.4)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(10)),.8)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(20),math.rad(0),math.rad(10)),.4)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(90),math.rad(0),math.rad(60)),.4)
end
CFuncs["Sound"].Create("rbxassetid://153092227", root, 5,1)
CFuncs["EchoSound"].Create("rbxassetid://153092227", root, 10, 1,0,10,0.25,0.5,1)
for i = 0,2,0.1 do
swait()
coroutine.resume(coroutine.create(function()
targetted.Head.CFrame = larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(-90),0,0)
for i,v in pairs(targetted:GetChildren()) do
if v:IsA("Part") or v:IsA("MeshPart") then
v.Velocity = vt(0,0,0)
end
end
end))
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(0)),.8)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(10),math.rad(0)),.8)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.25,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0),math.rad(0),math.rad(-80)),.8)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(80)),.8)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(20),math.rad(0),math.rad(10)),.8)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(90),math.rad(0),math.rad(-80)),.8)
end
CFuncs["EchoSound"].Create("rbxassetid://824687369", char, 1.5, 1,0,10,0.25,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://153092227", char, 1.5, 0.9,0,10,0.25,0.5,1)
for i = 0, 1 do
CFuncs["EchoSound"].Create("rbxassetid://1690476035", char, 1.5, 1,0.1,10,0.15,0.5,1)
end
CFuncs["EchoSound"].Create("rbxassetid://1690476035", root, 10, 1,0.1,10,0.15,0.5,1)
chatfunc("RRRRROOAGHH!",Color3.new(1,0,0),"Inverted","Antique",0.75)
for i = 0,4,0.1 do
swait()
coroutine.resume(coroutine.create(function()
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = targetted.Head.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-25000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(25000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(Color3.new(1,0,0))
trl.Lifetime = 5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
targetted.Head.CFrame = larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(-90),0,0)
CFuncs["Sound"].Create("rbxassetid://782353443", targetted.Head, 4,1)
CFuncs["Sound"].Create("rbxassetid://824687369", targetted.Head, 6, 1)
CFuncs["Sound"].Create("rbxassetid://153092227", targetted.Head,6,math.random(75,150)/150)
CFuncs["Sound"].Create("rbxassetid://163680447", targetted.Head, 3,math.random(75,150)/150)
CFuncs["Sound"].Create("rbxassetid://782354021", targetted.Head, 2.5,0.75)
sphere2(5,"Add",targetted.Head.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(3,3,3),-0.03,15,-0.03,MAINRUINCOLOR)
targetted:FindFirstChildOfClass("Humanoid").CameraOffset = vt(math.random(-10,10)/5,math.random(-10,10)/5,math.random(-10,10)/5)
for i = 0, 2 do
slash(5,5,true,"Round","Add","Out",targetted.Head.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(50,350)/250,BrickColor.new("Crimson"))
end
symbolizeBlink(targetted.Head,0,2092248396,Color3.new(1,0,0),math.random(3,35),0,0,0,targetted.Head,true,math.random(3,9),0.25)
for i,v in pairs(targetted:GetChildren()) do
if v:IsA("Part") or v:IsA("MeshPart") then
v.Velocity = vt(0,0,0)
end
end
end))
CamShakeAll(10,90,Character)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(0)),.8)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(10),math.rad(0)),.8)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.25,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0),math.rad(0),math.rad(-80)),.8)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-10),math.rad(0),math.rad(80)),.8)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(20),math.rad(0),math.rad(40)),.8)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(170),math.rad(0),math.rad(-30)),.8)
end
for i = 0, 49 do
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = targetted.Head.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-50000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(50000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(Color3.new(1,0.1,0.1))
trl.Lifetime = 5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
end
for i = 0, 49 do
sphere2(math.random(10,75)/10,"Add",targetted.Head.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(5,5,5),-0.05,50,-0.05,MAINRUINCOLOR)
slash(math.random(10,30)/15,5,true,"Round","Add","Out",targetted.Head.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(40,500)/250,BrickColor.new("Really red"))
end
CFuncs["EchoSound"].Create("rbxassetid://824687369", char, 2, 0.9,0,10,0.25,0.5,1)
for i = 0, 1 do
CFuncs["Sound"].Create("rbxassetid://221920821", targetted.Head, 5,0.9)
CFuncs["Sound"].Create("rbxassetid://221920821", targetted.Head, 7.5,0.75)
end
for i = 0, 4 do
CFuncs["Sound"].Create("rbxassetid://824687369", targetted.Head, 10, 1)
end
symbolizeBlink(targetted.Head,0,2109052855,Color3.new(1,0,0),30,0,0,0,root,false,0,1)
symbolizeBlink(targetted.Head,0,2109052855,Color3.new(1,0,0),30,0,0,0,root,false,0,2)
symbolizeBlink(targetted.Head,0,2109052855,Color3.new(1,0,0),30,0,0,0,root,false,0,4)
dmg(targetted)
CFuncs["Sound"].Create("rbxassetid://847061203", root, 2.5,1)
for i = 0, 9 do
sphereMK(3,0.25,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,10,-0.01,BrickColor.new("Really red"),0)
end
for i = 0, 24 do
PixelBlock(1,math.random(4,8),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),3,3,3,0.06,BrickColor.new("Really red"),0)
end
sphere(3,"Add",root.CFrame,vt(0,0,0),0.25,BrickColor.new("Really red"))
root.CFrame = originalpos
for i = 0, 9 do
sphereMK(3,0.25,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,10,-0.01,BrickColor.new("Really red"),0)
end
for i = 0, 24 do
PixelBlock(1,math.random(4,8),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),3,3,3,0.06,BrickColor.new("Really red"),0)
end
sphere(3,"Add",root.CFrame,vt(0,0,0),0.25,BrickColor.new("Really red"))
attack = false
hum.WalkSpeed = storehumanoidWS
end
end

function ColorEnbelived()
attack = true
hum.WalkSpeed = 0
chatfunc("Well color me convinced...",Color3.new(0,0,0),"Inverted","Antique",0.75)
for i = 0,50 do
swait()
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),3 + 1 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(13)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(24 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-13 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function PlaguedJump()
attack = true
local keptcolor = BrickColor.new("Forest green")
CFuncs["Sound"].Create("rbxassetid://1295446488", root, 10, 1)
CFuncs["EchoSound"].Create("rbxassetid://1657242236", char, 4, 1,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://1657242236", root, 10, 1,0,10,0.15,0.5,1)
for i = 0, 5 do
swait()
waveEff(math.random(10,100)/10,"Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(0,math.rad(math.random(-360,360)),0),vt(15,0.25,15),math.random(25,250)/250,0.25,BrickColor.new("White"))
slash(math.random(10,100)/10,3,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-360,360)),math.rad(math.random(-10,10))),vt(0.01,0.01,0.01),math.random(50,500)/250,BrickColor.new("White"))
end
CamShakeAll(20,100,char)
root.Velocity = vt(0,250,0) + root.CFrame.lookVector*250
wait(0.3)
repeat
swait()
for i = 0, 1, 0.6 do
		swait()
RH.C0=clerp(RH.C0,cf(1,-0.45,-0.45)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(-20)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(30)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.2,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(0)),.6)
RW.C0=clerp(RW.C0,cf(1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(-30)),.6)
LW.C0=clerp(LW.C0,cf(-1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(30)),.6)
end
for i = 0, 1, 0.6 do
		swait()
RH.C0=clerp(RH.C0,cf(1,-0.45,-0.45)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(-20)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(30)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.2,0)*angles(math.rad(90),math.rad(0),math.rad(0)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(0)),.6)
RW.C0=clerp(RW.C0,cf(1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(-30)),.6)
LW.C0=clerp(LW.C0,cf(-1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(30)),.6)
end
for i = 0, 1, 0.6 do
		swait()
RH.C0=clerp(RH.C0,cf(1,-0.45,-0.45)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(-20)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(30)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.2,0)*angles(math.rad(180),math.rad(0),math.rad(0)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(0)),.6)
RW.C0=clerp(RW.C0,cf(1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(-30)),.6)
LW.C0=clerp(LW.C0,cf(-1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(30)),.6)
end
for i = 0, 1, 0.6 do
		swait()
RH.C0=clerp(RH.C0,cf(1,-0.45,-0.45)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(-20)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(30)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.2,0)*angles(math.rad(270),math.rad(0),math.rad(0)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(0)),.6)
RW.C0=clerp(RW.C0,cf(1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(-30)),.6)
LW.C0=clerp(LW.C0,cf(-1.05,1.25,0)*angles(math.rad(190),math.rad(0),math.rad(30)),.6)
end
hfr,pfr=rayCast(root.Position,(CFrame.new(root.Position,root.Position - vt(0,1,0))).lookVector,4,char)
until hfr ~= nil
if hfr ~= nil then
local locat = Instance.new("Part", char)
locat.CanCollide = false
locat.FormFactor = 3
locat.Name = "Ring"
locat.Material = "Neon"
locat.Size = Vector3.new(1, 1, 1)
locat.Transparency = 1
locat.TopSurface = 0
locat.BottomSurface = 0
locat.Anchored = true
locat.CFrame = root.CFrame*CFrame.new(0,-3,0)
local poste = 0
local rotation = 0
local upperpos = 0
local rate = 0
local x = locat
CamShakeAll(50,100,char)
CFuncs["Sound"].Create("rbxassetid://847061203", char, 1,1)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 2.5, 1)
CFuncs["Sound"].Create("rbxassetid://1192402877", char, 2.5, 0.5)
sphere(1.25,"Add",root.CFrame,vt(0,0,0),16,MAINRUINCOLOR)
sphere(0.85,"Add",root.CFrame,vt(0,0,0),19,MAINRUINCOLOR)
for i = 0, 49 do
slash(math.random(10,13)/10,2,false,"Round","Add","Out",root.CFrame*CFrame.new(0,3,0)*CFrame.Angles(math.rad(math.random(-360, 360)), math.rad(math.random(-360, 360)), math.rad(math.random(-360, 360))),vt(0.05,0.01,0.05),math.random(9,14),MAINRUINCOLOR)
end
for i, v in pairs(FindNearestHead(Torso.CFrame.p, 280)) do
if v:FindFirstChild('Torso') then
dmg(v)
end
end
coroutine.resume(coroutine.create(function()
coroutine.resume(coroutine.create(function()
for i = 0, 119 do
sphere2(math.random(1,2),"Add",x.CFrame*CFrame.new(math.random(-350,350),0,math.random(-350,350)),vt(5,1,5),-0.05,math.random(50,250)/50,-0.05,keptcolor)
end
end))
for i = 0, 158 do
swait()
rotation = rotation + 5
poste = poste + 1
upperpos = upperpos + rate
rate = rate + 0.1
sphere2(8,"Add",x.CFrame*CFrame.Angles(0,math.rad(-rotation),0)*CFrame.new(0,upperpos/2,poste*2),vt(5+upperpos/10,5+upperpos/10,5+upperpos/10),-0.05,-0.05,-0.05,keptcolor)
sphere2(8,"Add",x.CFrame*CFrame.Angles(0,math.rad(90-rotation),0)*CFrame.new(0,upperpos/2,poste*2),vt(5+upperpos/10,5+upperpos/10,5+upperpos/10),-0.05,-0.05,-0.05,keptcolor)
sphere2(8,"Add",x.CFrame*CFrame.Angles(0,math.rad(180-rotation),0)*CFrame.new(0,upperpos/2,poste*2),vt(5+upperpos/10,5+upperpos/10,5+upperpos/10),-0.025,-0.025,-0.025,keptcolor)
sphere2(8,"Add",x.CFrame*CFrame.Angles(0,math.rad(270-rotation),0)*CFrame.new(0,upperpos/2,poste*2),vt(5+upperpos/10,5+upperpos/10,5+upperpos/10),-0.025,-0.025,-0.025,keptcolor)
end
x:Destroy()
end))
end
attack = false
end

function CrazedInsanity()
attack = true
for i = 0,30 do
swait()
sphere2(8,"Add",rleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Navy blue"),BrickColor.new("Navy blue").Color)
sphere2(8,"Add",lleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Really black"),BrickColor.new("Really black").Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3 + MRANDOM(-3,3)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 6 * math.cos(sine / 39) + MRANDOM(-5,5))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3 + MRANDOM(-3,3)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 45) + MRANDOM(-5,5))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 47),0 - 0.25 * math.cos(sine / 40),7 + 1 * math.cos(sine / 32))*angles(math.rad(-15 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(45)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(45)),.5)
RW.C0=clerp(RW.C0,cf(1.45,0.5,0)*angles(math.rad(90),math.rad(0),math.rad(50)),.5)
LW.C0=clerp(LW.C0,cf(-1.45,0.5,0.1)*angles(math.rad(20),math.rad(10),math.rad(-30)),.5)
end
coroutine.resume(coroutine.create(function()
for i = 0, 2 do
swait()
for i = 0, 2 do
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 10,1)
CFuncs["LongSound"].Create("rbxassetid://763717897", char, 10, 0.5)
CFuncs["LongSound"].Create("rbxassetid://763717897", char, 7.5, 0.25)
CFuncs["Sound"].Create("rbxassetid://763718160", char, 10, 0.9)
CFuncs["Sound"].Create("rbxassetid://782353443", char, 10, 0.5)
CFuncs["Sound"].Create("rbxassetid://335657174", char, 5, 0.75)
CFuncs["LongSound"].Create("rbxassetid://335657174", char, 10, 0.25)
CFuncs["Sound"].Create("rbxassetid://167115397", char, 10, 1)
CFuncs["LongSound"].Create("rbxassetid://167115397", char, 10, 0.75)
CFuncs["LongSound"].Create("rbxassetid://167115397", char, 10, 0.5)
end
for i = 0, 2 do
block(3,"Add",root.CFrame,vt(1,1,1),6.5*2,6.5*2,6.5*2,BrickColor.new("Dark blue"),BrickColor.new("Dark blue").Color)
block(2,"Add",root.CFrame,vt(1,1,1),6*2,6*2,6*2,BrickColor.new("Really Black"),BrickColor.new("Really Black").Color)
block(1,"Add",root.CFrame,vt(1,1,1),4.5*2,4.5*2,4.5*2,BrickColor.new("Dark blue"),BrickColor.new("Dark blue").Color)
end
for i = 0, 49 do
slash(math.random(10,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(150,2500)/250,BrickColor.new("Really black"))
end
CamShakeAll(20,120,char)
for i = 0, 199 do
swait()
coroutine.resume(coroutine.create(function()
for i, v in pairs(FindNearestHead(root.CFrame.p, 250)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
end))
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = root.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-25000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(25000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(MAINRUINCOLOR.Color)
trl.Lifetime = 5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
sphere2(15,"Add",root.CFrame,vt(1.25,1.25,1.25),2.5,2.5,2.5,BrickColor.new("Dark blue"))
for i = 0, 2 do
	CamShakeAll(20,120,char)
slash(15,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),25,BrickColor.new("Dark blue"))
slash(15,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),50,BrickColor.new("Really black"))
end
end
end
end))
for i = 0,60,0.1 do
swait()
sphere2(8,"Add",rleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Navy blue"),BrickColor.new("Navy blue").Color)
sphere2(8,"Add",lleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Really black"),BrickColor.new("Really black").Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3 + MRANDOM(-3,3)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 6 * math.cos(sine / 39) + MRANDOM(-5,5))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3 + MRANDOM(-3,3)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 45) + MRANDOM(-5,5))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 47),0 - 0.25 * math.cos(sine / 40),7 + 1 * math.cos(sine / 32))*angles(math.rad(7.5 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-15)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(30),math.rad(0),math.rad(-15)),.5)
RW.C0=clerp(RW.C0,cf(1.45,0.5,0)*angles(math.rad(85),math.rad(0),math.rad(-20)),.5)
LW.C0=clerp(LW.C0,cf(-1.45,0.5,0.1)*angles(math.rad(20),math.rad(10),math.rad(-30)),.5)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function RainbowBeam()
attack = true
CFuncs["Sound"].Create("rbxassetid://136007472", root, 2, 1.7)
local rngb = Instance.new("Part", char)
        rngb.Anchored = true
        rngb.BrickColor = origcolor
        rngb.CanCollide = false
        rngb.FormFactor = 3
        rngb.Name = "Ring"
        rngb.Material = "Neon"
        rngb.Size = Vector3.new(1, 0.05, 1)
        rngb.Transparency = 1
        rngb.TopSurface = 0
        rngb.BottomSurface = 0
        local rngmb = Instance.new("SpecialMesh", rngb)
        rngmb.MeshType = "Brick"
rngmb.Name = "SizeMesh"
rngmb.Scale = vt(0,1,0)
local obj1 = script.chring2:Clone()
obj1.Parent = char
obj1.Transparency = 1
obj1.Size = vt(1,1,1)
obj1.Color = BrickColor.new("White").Color
if rainbowmode == true then
obj1.Color = Color3.new(r/255,g/255,b/255)
end
local orb = rngb:Clone()
orb.Parent = char
orb.Transparency = 0
orb.BrickColor = BrickColor.new("White")
orb.Size = vt(1,1,1)
local orbmish = orb.SizeMesh
orbmish.Scale = vt(0,0,0)
orbmish.MeshType = "Sphere"

local orbe = rngb:Clone()
orbe.Parent = char
orbe.Transparency = 0.5
orbe.BrickColor = BrickColor.new("New Yeller")
orbe.Size = vt(1,1,1)
local orbmish2 = orbe.SizeMesh
orbmish2.Scale = vt(0,0,0)
orbmish2.MeshType = "Sphere"
orbe.Color = Color3.new(r/255,g/255,b/255)

rngb:Destroy()
CFuncs["Sound"].Create("rbxassetid://136007472", orb, 1.5, 1)
local rval = 0
local eval = 1
coroutine.resume(coroutine.create(function()
	for i = 0,7,0.1 do
	swait()
rval = rval + math.random(30,40)
eval = eval + 0.45
obj1.CFrame = root.CFrame*CFrame.new(0,1,-5)*CFrame.Angles(math.rad(0),math.rad(0),math.rad(rval))
	end
end))
RootPart.CFrame = FaceMouse()[1]
for i = 0,5,0.1 do
swait()
if rainbowmode == true then
orbe.Color = Color3.new(r/255,g/255,b/255)
end
orb.CFrame = root.CFrame*CFrame.new(0,0.5,0) + root.CFrame.lookVector*11.5
orbe.CFrame = root.CFrame*CFrame.new(0,0.5,0) + root.CFrame.lookVector*11.5
rval = rval + math.random(30,40)
eval = eval + 0.45
obj1.Transparency = obj1.Transparency - 0.005
obj1.Size = obj1.Size + vt(0.7,0.7,0.1)
obj1.CFrame = root.CFrame*CFrame.new(0,1,-5)*CFrame.Angles(math.rad(0),math.rad(0),math.rad(rval))

            RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0,0,0)* angles(math.rad(0),math.rad(0),math.rad(90)),0.3)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(0),math.rad(0),math.rad(-90)),.3)
RW.C0 = clerp(RW.C0, CFrame.new(1.5, 0.5, 0) * angles(math.rad(90), math.rad(0), math.rad(90)), 0.3)
LW.C0 = clerp(LW.C0, CFrame.new(-1.5, 0.5, 0) * angles(math.rad(10), math.rad(0), math.rad(-20)), 0.3)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 25),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-0.5),math.rad(0),math.rad(0)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 25),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(10),math.rad(0)),.3)

end
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",obj1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
coroutine.resume(coroutine.create(function()
while true do
	swait()
eff.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
end
end))
eff.Rate = 27500
eff.Lifetime = NumberRange.new(1,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,60,0),NumberSequenceKeypoint.new(0.2,3,0),NumberSequenceKeypoint.new(0.8,24,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.2,0,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(100,650)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.35)
eff.Enabled = false
end))
orbe.Transparency = 1
orb.Transparency = 1
orb.CFrame = root.CFrame*CFrame.new(0,0.5,0) + root.CFrame.lookVector*5
CFuncs["Sound"].Create("rbxassetid://294188875", char, 1, 1)
local a = Instance.new("Part",Character)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("White")
a.Material = "Neon"
a.Transparency = 0
a.Shape = "Cylinder"
	a.CanCollide = false
local a2 = Instance.new("Part",Character)
	a2.Name = "Direction"	
	a2.Anchored = true
	a2.BrickColor = bc("New Yeller")
a2.Color = Color3.new(r/255,g/255,b/255)
a2.Material = "Neon"
a2.Transparency = 0.5
a2.Shape = "Cylinder"
	a2.CanCollide = false
local ba = Instance.new("Part",Character)
	ba.Name = "HitDirect"	
	ba.Anchored = true
	ba.BrickColor = bc("Cool yellow")
ba.Material = "Neon"
ba.Transparency = 1
	ba.CanCollide = false
	local ray = Ray.new(
	    orb.CFrame.p,                           -- origin
	    (mouse.Hit.p - orb.CFrame.p).unit * 1000 -- direction
	) 
	local ignore = Character
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	a2.BottomSurface = 10
	a2.TopSurface = 10
	local distance = (orb.CFrame.p - position).magnitude
	a.Size = Vector3.new(distance, 1, 1)
	a.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance/2)
	a2.Size = Vector3.new(distance, 1, 1)
	a2.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance/2)
ba.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance)
a.CFrame = a.CFrame*CFrame.Angles(0,math.rad(90),0)
a2.CFrame = a2.CFrame*CFrame.Angles(0,math.rad(90),0)
game:GetService("Debris"):AddItem(a, 20)
game:GetService("Debris"):AddItem(a2, 20)
game:GetService("Debris"):AddItem(ba, 20)
local msh = Instance.new("SpecialMesh",a)
msh.MeshType = "Cylinder"
msh.Scale = vt(1,5*5,5*5)
local msh2 = Instance.new("SpecialMesh",a2)
msh2.MeshType = "Cylinder"
msh2.Scale = vt(1,6*5,6*5)
coroutine.resume(coroutine.create(function()
for i = 0,10,0.1 do
swait()
shakes(0.2,0.8)
rval = rval + math.random(30,40)
eval = eval + 0.45
obj1.Transparency = obj1.Transparency - 0.005
obj1.Size = obj1.Size + vt(0,0,0.075)
obj1.Size = obj1.Size - vt(0.125,0.125,0)
obj1.CFrame = root.CFrame*CFrame.new(0,1,-5)*CFrame.Angles(math.rad(0),math.rad(0),math.rad(rval))
a2.Color = Color3.new(r/255,g/255,b/255)
orb.CFrame = root.CFrame*CFrame.new(0,0.5,0) + root.CFrame.lookVector*4
orbe.CFrame = root.CFrame*CFrame.new(0,0.5,0) + root.CFrame.lookVector*4
ray = Ray.new(
	    orb.CFrame.p,                           -- origin
	    (mouse.Hit.p - orb.CFrame.p).unit * 1000 -- direction
	) 
hit, position, normal = workspace:FindPartOnRay(ray, ignore)
distance = (orb.CFrame.p - position).magnitude
RW.C0 = clerp(RW.C0, CFrame.new(1.5, 0.5, 0) * angles(math.rad(90), math.rad(0), math.rad(90)), 0.3)
LW.C0 = clerp(LW.C0, CFrame.new(-1.5, 0.5, 0) * angles(math.rad(10), math.rad(0), math.rad(-20)), 0.3)
a.Size = Vector3.new(distance, 1, 1)
a.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance/2)
a2.Size = Vector3.new(distance, 1, 1)
a2.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance/2)
ba.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance)
a.CFrame = a.CFrame*CFrame.Angles(0,math.rad(90),0)
a2.CFrame = a2.CFrame*CFrame.Angles(0,math.rad(90),0)
msh.Scale = msh.Scale - vt(0,0.025*5,0.025*5)
msh2.Scale = msh2.Scale - vt(0,0.03*5,0.03*5)
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",ba)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
eff.Rate = 50
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,60,0),NumberSequenceKeypoint.new(0.2,3,0),NumberSequenceKeypoint.new(0.8,24,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(50,450)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.25)
eff.Enabled = false
end))
sphereMK(5,1.5,"Add",ba.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),15*2,15*2,25*2,-0.15,MAINRUINCOLOR,0)
sphereMK(5,1.5,"Add",ba.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),15*2,15*2,25*2,-0.15,MAINRUINCOLOR,0)
MagniDamage(ba, 30*2, 10,20, 0, "Normal")
end
attack = false
	for i = 0,49 do
		swait()
		msh.Scale = msh.Scale - vt(0,0.05*5,0.05*5)
        msh2.Scale = msh2.Scale - vt(0,0.06*5,0.06*5)
		orbe.Transparency = obj1.Transparency + 0.02
		orb.Transparency = obj1.Transparency + 0.02
		obj1.Transparency = obj1.Transparency + 0.02
		obj1.Size = obj1.Size + vt(0,-0.5,-0.5)
	end
a:Destroy()
a2:Destroy()
orb:Destroy()
orbe:Destroy()
wait(3)
obj1:Destroy()
ba:Destroy()
end))
end


function CorruptionEvent()
attack = true
hum.WalkSpeed = 0
CFuncs["Sound"].Create("rbxassetid://838392947", root, 10, 1)
CFuncs["Sound"].Create("rbxassetid://1368598393", root, 10, 1)
local keptcolor = MAINRUINCOLOR
for i = 0,4,0.1 do
swait()
CamShakeAll(10,45,Character)
block(10,"Add",rleg.CFrame*CFrame.new(0,-1,0),vt(1,1,1),0.01,0.01,0.01,MAINRUINCOLOR,MAINRUINCOLOR.Color)
RH.C0=clerp(RH.C0,cf(1,-0.15,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(-5),math.rad(-20)),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(1),math.rad(20)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.25,-0.05)*angles(math.rad(-20),math.rad(0),math.rad(10)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-10)),.1)
RW.C0=clerp(RW.C0,cf(1.45,0.5,0.1)*angles(math.rad(-5),math.rad(-10),math.rad(20)),.1)
LW.C0=clerp(LW.C0,cf(-1.4,0.5,0.1)*angles(math.rad(-5),math.rad(10),math.rad(-20)),.1)
end
symbolizeBlink(root,0,2109052855,MAINRUINCOLOR.Color,25,0,0,0,root,false,0,1)
symbolizeBlink(root,0,2109052855,MAINRUINCOLOR.Color,25,0,0,0,root,false,0,1.5)
symbolizeBlink(root,0,2109052855,MAINRUINCOLOR.Color,25,0,0,0,root,false,0,3)
CFuncs["Sound"].Create("rbxassetid://1368637781", root, 3,1)
CFuncs["Sound"].Create("rbxassetid://763718160", root, 4, 1.1)
CFuncs["Sound"].Create("rbxassetid://782353443", root, 6, 1)
CFuncs["EchoSound"].Create("rbxassetid://824687369", root, 10, 1.1,0,10,0.25,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://824687369", char, 1.5, 1.1,0,10,0.25,0.5,1)
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",cen)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 10000000
eff.Enabled = true
eff.EmissionDirection = "Front"
eff.Lifetime = NumberRange.new(2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,120,0),NumberSequenceKeypoint.new(0.1,40,0),NumberSequenceKeypoint.new(0.8,80,0),NumberSequenceKeypoint.new(1,140,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(500)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.2)
eff.Enabled = false
wait(5)
eff:Destroy()
	end))
sphere2(5,"Add",root.CFrame*CFrame.new(0,-3,0),vt(10,1,10),1,0.01,1,MAINRUINCOLOR,MAINRUINCOLOR.Color)
sphere2(5,"Add",root.CFrame*CFrame.new(0,-3,0),vt(10,1,10),2,0.01,2,MAINRUINCOLOR,MAINRUINCOLOR.Color)
for i = 0, 24 do
slash(math.random(15,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(200,500)/250,BrickColor.new("Really black"))
end
local rrot = 0
coroutine.resume(coroutine.create(function()
for i = 0, 4 do
rrot = rrot + 45
local xa = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
xa.Anchored = true
local xb = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
xb.Anchored = true
local xc = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
xc.Anchored = true
local xd = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
xd.Anchored = true
CFuncs["Sound"].Create("rbxassetid://824687369", xa, 1,0.75)
CFuncs["Sound"].Create("rbxassetid://822968467", xa, 2,0.95)
CFuncs["Sound"].Create("rbxassetid://822969951", xa, 3,1)
CFuncs["Sound"].Create("rbxassetid://824687369", xb, 1,0.75)
CFuncs["Sound"].Create("rbxassetid://822968467", xb, 2,0.95)
CFuncs["Sound"].Create("rbxassetid://822969951", xb, 3,1)
CFuncs["Sound"].Create("rbxassetid://824687369", xc, 1,0.75)
CFuncs["Sound"].Create("rbxassetid://822968467", xc, 2,0.95)
CFuncs["Sound"].Create("rbxassetid://822969951", xc, 3,1)
CFuncs["Sound"].Create("rbxassetid://824687369", xd, 1,0.75)
CFuncs["Sound"].Create("rbxassetid://822968467", xd, 2,0.95)
CFuncs["Sound"].Create("rbxassetid://822969951", xd, 3,1)
xa.CFrame = root.CFrame*CFrame.Angles(0,math.rad(rrot),0)*CFrame.new(0,-3,-rrot/1.75)
xb.CFrame = root.CFrame*CFrame.Angles(0,math.rad(rrot),0)*CFrame.new(0,-3,rrot/1.75)
xc.CFrame = root.CFrame*CFrame.Angles(0,math.rad(rrot),0)*CFrame.new(-rrot/1.75,-3,0)
xd.CFrame = root.CFrame*CFrame.Angles(0,math.rad(rrot),0)*CFrame.new(rrot/1.75,-3,0)
MagniDamage(xa, 30, 39*rrot/5,65*rrot/2.5, 0, "Normal")
MagniDamage(xb, 30, 39*rrot/5,65*rrot/2.5, 0, "Normal")
MagniDamage(xc, 30, 39*rrot/5,65*rrot/2.5, 0, "Normal")
MagniDamage(xd, 30, 39*rrot/5,65*rrot/2.5, 0, "Normal")
for i = 0, 9 do
slash(math.random(15,50)/10,5,true,"Round","Add","Out",xa.CFrame*CFrame.new(0,-1.5,0)*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(0.01,0.01,0.01),math.random(50,125)/250,BrickColor.new("Really black"))
slash(math.random(15,50)/10,5,true,"Round","Add","Out",xb.CFrame*CFrame.new(0,-1.5,0)*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(0.01,0.01,0.01),math.random(50,125)/250,BrickColor.new("Really black"))
slash(math.random(15,50)/10,5,true,"Round","Add","Out",xc.CFrame*CFrame.new(0,-1.5,0)*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(0.01,0.01,0.01),math.random(50,125)/250,BrickColor.new("Really black"))
slash(math.random(15,50)/10,5,true,"Round","Add","Out",xd.CFrame*CFrame.new(0,-1.5,0)*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(0.01,0.01,0.01),math.random(50,125)/250,BrickColor.new("Really black"))
end
block(1.5,"Add",xa.CFrame*CFrame.new(0,-10,0),vt(30,30,30),0.3,0.3,0.3,keptcolor,keptcolor.Color)
block(1.5,"Add",xb.CFrame*CFrame.new(0,-10,0),vt(30,30,30),0.3,0.3,0.3,keptcolor,keptcolor.Color)
block(1.5,"Add",xc.CFrame*CFrame.new(0,-10,0),vt(30,30,30),0.3,0.3,0.3,keptcolor,keptcolor.Color)
block(1.5,"Add",xd.CFrame*CFrame.new(0,-10,0),vt(30,30,30),0.3,0.3,0.3,keptcolor,keptcolor.Color)
sphere2(2,"Add",xa.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(25,1,25),0.05,1.5,0.05,keptcolor,keptcolor.Color)
sphere2(2,"Add",xb.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(25,1,25),0.05,1.5,0.05,keptcolor,keptcolor.Color)
sphere2(2,"Add",xc.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(25,1,25),0.05,1.5,0.05,keptcolor,keptcolor.Color)
sphere2(2,"Add",xd.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(25,1,25),0.05,1.5,0.05,keptcolor,keptcolor.Color)
sphere2(4,"Add",xa.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(30,1,30),0.05,1.5,0.05,BrickColor.new("Really black"),Color3.new(0,0,0))
sphere2(4,"Add",xb.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(30,1,30),0.05,1.5,0.05,BrickColor.new("Really black"),Color3.new(0,0,0))
sphere2(4,"Add",xc.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(30,1,30),0.05,1.5,0.05,BrickColor.new("Really black"),Color3.new(0,0,0))
sphere2(4,"Add",xd.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(30,1,30),0.05,1.5,0.05,BrickColor.new("Really black"),Color3.new(0,0,0))
game:GetService("Debris"):AddItem(xa, 5)
game:GetService("Debris"):AddItem(xb, 5)
game:GetService("Debris"):AddItem(xc, 5)
game:GetService("Debris"):AddItem(xd, 5)
coroutine.resume(coroutine.create(function()
for i = 0, 19 do
swait()
CamShakeAll(10,75,Character)
end
end))
swait(9)
end
end))
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0),math.rad(10)),.8)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(1),math.rad(10)),.8)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.25,-0.05)*angles(math.rad(10),math.rad(0),math.rad(0)),.8)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(40),math.rad(0),math.rad(0)),.8)
RW.C0=clerp(RW.C0,cf(1.45,0.5,0.1)*angles(math.rad(-35),math.rad(-10),math.rad(60)),.8)
LW.C0=clerp(LW.C0,cf(-1.4,0.5,0.1)*angles(math.rad(-35),math.rad(10),math.rad(-50)),.8)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function HolyBarrier()
	attack = true
	shielding = true
	CFuncs["Sound"].Create("rbxassetid://1368583274", root, 7.5, 1)
	for i = 0, 2, 0.1 do
        swait()
		slash(math.random(30,100)/10,5,true,"Round","Add","Out",Torso.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-1, 1)), math.rad(math.random(-360, 360)), math.rad(math.random(-360, 360))),vt(0.05,0.01,0.05),math.random(50,60)/250,MAINRUINCOLOR)
		RH.C0=clerp(RH.C0,cf(1,-1 - 0.1 * math.cos(sine / 32),0)*angles(math.rad(60),math.rad(90),math.rad(0))*angles(math.rad(0),math.rad(0),math.rad(-2 - 1 * math.cos(sine / 32))),.4)
		LH.C0=clerp(LH.C0,cf(-1,-1 - 0.1 * math.cos(sine / 32),0)*angles(math.rad(-20),math.rad(-90),math.rad(0))*angles(math.rad(-3 + 1 * math.cos(sine / 32)),math.rad(0),math.rad(-10)),.4)
		RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,-0.5 + 0.1 * math.cos(sine / 32))*angles(math.rad(30),math.rad(0),math.rad(0)),.4)
		Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-30),math.rad(0),math.rad(0)),.4)
		RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(120),math.rad(-230),math.rad(-40)),.1)
		LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(120),math.rad(230),math.rad(40)),.1)
	end
	CFuncs["Sound"].Create("rbxassetid://1368637781", root, 7, 1)
    stash = Instance.new("Model",workspace)
	local barrier = Instance.new("Part",stash)
	barrier.Anchored = true
	barrier.CanCollide = false
	barrier.Size = Vector3.new(20,20,20)
	barrier.CFrame = root.CFrame
	barrier.Transparency = 0.4
	barrier.BrickColor = MAINRUINCOLOR
	barrier.Material = Enum.Material.Neon
	barrier.Shape = Enum.PartType.Ball
	barrier.CFrame = root.CFrame
	sphere(3, "Add", root.CFrame, vt(20, 20, 20), 0.15, MAINRUINCOLOR)
	Torso.Anchored = true
	for i=1,50 do
		local p = Instance.new("Part",stash)
		p.Anchored = true
		p.Transparency = 1
		p.Size = Vector3.new(11,11,11)
		p.CFrame = root.CFrame * CFrame.Angles(math.random(0,360),math.random(0,360),math.random(0,360))
	end
	repeat
		swait(5)
        CamShakeAll(10,7,Character)
		waveEff(5,"Add","In",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(0,math.rad(math.random(-360,360)),0),vt(15,0.25,15),-0.075,0.05,BrickColor.new("Deep orange"))
		slash(math.random(30,100)/10,1,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-1,1)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(1,50)/250,MAINRUINCOLOR)
		slash(math.random(30,100)/10,1,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-1,1)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(1,50)/250,MAINRUINCOLOR)
		slash(math.random(30,100)/10,1,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-1,1)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(1,50)/250,MAINRUINCOLOR)
		sphereMK(3,0.5,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,10,-0.01,BrickColor.new("White"),0)
	until shielding == false
	stash:Remove()
	Torso.Anchored = false
	shielding = false
	sphere(3, "Add", root.CFrame, vt(20, 20, 20), 0.15, MAINRUINCOLOR)
	attack = false
end

function EndGROUND()
	attack = true
hum.WalkSpeed = 0
bosschatfunc("THIS IS IT!",MAINRUINCOLOR.Color,1)
CFuncs["Sound"].Create("rbxassetid://838392947", root, 10, 1)
CFuncs["Sound"].Create("rbxassetid://1368598393", root, 10, 1)
CFuncs["EchoSound"].Create("rbxassetid://1690475123", char, 1.5, 1,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://1690475123", root, 10, 1,0,10,0.15,0.5,1)
local keptcolor = MAINRUINCOLOR
for i = 0,4,0.1 do
swait()
block(10,"Add",rarm.CFrame*CFrame.new(0,-6,0),vt(4,4,4),0.05,0.05,0.05,MAINRUINCOLOR,MAINRUINCOLOR.Color)
slash(math.random(25,50)/10,5,true,"Round","Add","Out",rarm.CFrame*CFrame.new(0,-6,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.1,0.01,0.1),-0.1,BrickColor.new("Really black"))
RH.C0=clerp(RH.C0,cf(1,-0.15,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(-15),math.rad(-20)),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(1),math.rad(20)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.25,-0.05)*angles(math.rad(-20),math.rad(0),math.rad(30)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-30)),.1)
RW.C0=clerp(RW.C0,cf(1.45,0.5,0.1)*angles(math.rad(170),math.rad(-5),math.rad(10)),.1)
LW.C0=clerp(LW.C0,cf(-1.4,0.5,0.1)*angles(math.rad(-5),math.rad(10),math.rad(-20)),.1)
end
symbolizeBlink(root,0,2109052855,MAINRUINCOLOR.Color,25,0,0,0,root,false,0,1)
CFuncs["Sound"].Create("rbxassetid://1368637781", root, 3,1)
CFuncs["Sound"].Create("rbxassetid://763718160", root, 4, 1.1)
CFuncs["Sound"].Create("rbxassetid://782353443", root, 6, 1)
CFuncs["EchoSound"].Create("rbxassetid://824687369", root, 10, 1,0,10,0.25,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://824687369", char, 2, 1,0,10,0.25,0.5,1)
coroutine.resume(coroutine.create(function()
CamShakeAll(40,100,Character)
local eff = Instance.new("ParticleEmitter",cen)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 10000000
eff.Enabled = true
eff.EmissionDirection = "Front"
eff.Lifetime = NumberRange.new(2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,120,0),NumberSequenceKeypoint.new(0.1,40,0),NumberSequenceKeypoint.new(0.8,80,0),NumberSequenceKeypoint.new(1,140,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(500)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.2)
eff.Enabled = false
wait(5)
eff:Destroy()
end))
sphere2(5,"Add",root.CFrame*CFrame.new(0,-3,0),vt(10,1,10),1,0.01,1,MAINRUINCOLOR,MAINRUINCOLOR.Color)
sphere2(5,"Add",root.CFrame*CFrame.new(0,-3,0),vt(10,1,10),2,0.01,2,MAINRUINCOLOR,MAINRUINCOLOR.Color)
for i = 0, 24 do
slash(math.random(15,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(200,500)/250,BrickColor.new("Really black"))
end
local rrot = 0
local xam = 1
coroutine.resume(coroutine.create(function()
for i = 0, 14 do
swait()
rrot = rrot + 40*xam
xam = xam + 0.25
local bonus = xam
local xa = CreateParta(char,0.5,1,"Neon",BrickColor.random())
xa.Anchored = true
xa.Color = Color3.new(0,0,0)
xa.CFrame = root.CFrame*CFrame.new(0,-3,-rrot/1.75)
CreateMesh(xa,"Sphere",30*bonus,1,30*bonus)
local xc = 0
coroutine.resume(coroutine.create(function()
for i = 0, 99 do
	swait()
	xc = xc + 0.01
	xa.Color = Color3.new(xc,0,0)
end
xa.Transparency = 1
CFuncs["Sound"].Create("rbxassetid://331666100", xa, 5,0.75)
MagniDamage(xa, 30*bonus, 78*bonus,99*bonus, 0, "Normal")
for i = 0, 9 do
slash(math.random(15,50)/10,5,true,"Round","Add","Out",xa.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(0.01*bonus,0.01,0.01*bonus),math.random(50,125)/250*bonus,BrickColor.new("Really black"))
end
CamShakeAll(20,100,xa)
block(1.5,"Add",xa.CFrame*CFrame.new(0,-10,0),vt(30*bonus,30*bonus,30*bonus),0.3,0.3,0.3,keptcolor,keptcolor.Color)
sphere2(2,"Add",xa.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(25*bonus,1,25*bonus),0.05*bonus,1.5*bonus,0.05*bonus,keptcolor,keptcolor.Color)
sphere2(4,"Add",xa.CFrame*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),vt(30*bonus,1,30*bonus),0.05*bonus,1.5*bonus,0.05*bonus,BrickColor.new("Really black"),Color3.new(0,0,0))
game:GetService("Debris"):AddItem(xa, 5)
end))
end
end))
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(-25),math.rad(30)),.8)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(1),math.rad(20)),.8)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.25,-0.5)*angles(math.rad(30),math.rad(0),math.rad(50)),.8)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(-50)),.8)
RW.C0=clerp(RW.C0,cf(1.45,0.5,0.1)*angles(math.rad(35),math.rad(-10),math.rad(30)),.8)
LW.C0=clerp(LW.C0,cf(-1.4,0.5,0.1)*angles(math.rad(-35),math.rad(10),math.rad(-50)),.8)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function ClearDisk()
attack = true
hum.WalkSpeed = 2
local keptcolor = MAINRUINCOLOR
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("Clear Disks!",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("TAKE THAT!!!",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("Hey!",MAINRUINCOLOR.Color,1)
end
CFuncs["Sound"].Create("rbxassetid://847061203", root, 2, 1)
CFuncs["EchoSound"].Create("rbxassetid://1625448638", root, 4, 1,0,10,0.15,0.5,1)
sphere2(5,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(1,1,1),0.1,0.1,0.1,keptcolor,keptcolor.Color)
sphere2(5,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(1,1,1),0.2,0.2,0.2,keptcolor,keptcolor.Color)
for i = 0, 14 do
PixelBlock(1,math.random(1,3),"Add",larm.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.02,BrickColor.new("Pastel Blue"),0)
end
for i = 0,2,0.1 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(-60)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.3)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.3)
end
CFuncs["Sound"].Create("rbxassetid://763755889", root, 2.5,1.1)
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(90)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(180)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(270)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
local rot = 25
for i = 0, 3 do
local dis = CreateParta(char,0.5,1,"Neon",BrickColor.new("Pastel Blue"))
CFuncs["EchoSound"].Create("rbxassetid://763718160", dis, 3, 1.1,0,10,0.15,0.5,1)
dis.CFrame = root.CFrame*CFrame.new(0,2,-3)
dis.Transparency = 0.5
CreateMesh(dis,"Sphere",10,1,10)
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-5,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(5,0,0)
local trl = Instance.new('Trail',wed)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 0.6
local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
dis.CFrame = dis.CFrame*CFrame.Angles(0,math.rad(rot),0)
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*250
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
local hitted = false
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
    CamShakeAll(20,35,Character)
	CFuncs["EchoSound"].Create("rbxassetid://782200047", dis, 7, 1.1,0,10,0.15,0.5,1)
	MagniDamage(dis, 30, 82,34575, 0, "Normal")
	sphere2(8,"Add",dis.CFrame,vt(10,1,10),1,0.1,1,keptcolor,keptcolor.Color)
	sphere2(4,"Add",dis.CFrame,vt(1,1,1),0.5,0.5,0.5,keptcolor,keptcolor.Color)
	sphere2(3,"Add",dis.CFrame,vt(1,1,1),0.5,0.5,0.5,BrickColor.new("White"),Color3.new(1,1,1))
	coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 10000000
eff.Enabled = true
eff.EmissionDirection = "Front"
eff.Lifetime = NumberRange.new(1)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,75,0),NumberSequenceKeypoint.new(0.1,20,0),NumberSequenceKeypoint.new(0.8,40,0),NumberSequenceKeypoint.new(1,60,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(150)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.2)
eff.Enabled = false
	end))
	coroutine.resume(coroutine.create(function()
		for i = 0, 9 do
local disr = CreateParta(char,1,1,"Neon",keptcolor)
disr.CFrame = dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",disr)
at1.Position = vt(-2,0,0)
local at2 = Instance.new("Attachment",disr)
at2.Position = vt(2,0,0)
local trl = Instance.new('Trail',disr)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://2342682798"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(Color3.new(0.3,1,1))
trl.Lifetime = 0.5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = disr.CFrame.lookVector*math.random(50,200)
bv.Parent = disr
local val = 0
coroutine.resume(coroutine.create(function()
	swait(30)
	for i = 0, 9 do
		swait()
		val = val + 0.1
		trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, val),NumberSequenceKeypoint.new(1, 1)})
	end
game:GetService("Debris"):AddItem(disr, 3)
end))
end
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(Color3.new(0.3,1,1))
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.25)
eff.Enabled = false
end))
	for i = 0, 9 do
		slash(math.random(10,20)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(100,200)/250,BrickColor.new("White"))
	end
for i = 0, 19 do
PixelBlock(1,math.random(5,20),"Add",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),4,4,4,0.08,BrickColor.new("Pastel Blue"),0)
end
dis.Anchored = true
dis.Transparency = 1
wait(8)
dis:Destroy()
end
end)
end))
rot = rot - 15
end
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(-30),math.rad(0)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(5)),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(60)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(-50)),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(90),math.rad(0),math.rad(-60)),.3)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function HeavenlyDisk()
attack = true
hum.WalkSpeed = 2
local keptcolor = MAINRUINCOLOR
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("Dont make this too easy for you.",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("Heavenly Disks!",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("Take it!",MAINRUINCOLOR.Color,1)
end
CFuncs["Sound"].Create("rbxassetid://847061203", root, 2, 1)
CFuncs["EchoSound"].Create("rbxassetid://1625448638", root, 4, 1,0,10,0.15,0.5,1)
sphere2(5,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(1,1,1),0.1,0.1,0.1,keptcolor,keptcolor.Color)
sphere2(5,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(1,1,1),0.2,0.2,0.2,keptcolor,keptcolor.Color)
for i = 0, 14 do
PixelBlock(1,math.random(1,3),"Add",larm.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.02,BrickColor.new("Toothpaste"),0)
end
for i = 0,2,0.1 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(-60)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.3)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.3)
end
CFuncs["Sound"].Create("rbxassetid://763755889", root, 2.5,1.1)
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(90)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(180)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
for i = 0,1,0.6 do
swait()
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-2,0)*CFrame.Angles(math.rad(90),0,0),vt(2.25,0.1,2.25),0.01,0.01,0.01,keptcolor,keptcolor.Color)
slash(math.random(15,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.05,0.01,0.05),math.random(25,75)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-5)),.6)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(30),math.rad(0)),.6)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(270)),.6)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(20),math.rad(0),math.rad(30)),.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.6)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(60)),.6)
end
local rot = 15
for i = 0, 2 do
local dis = CreateParta(char,0.5,1,"Neon",BrickColor.new("Toothpaste"))
CFuncs["EchoSound"].Create("rbxassetid://763718160", dis, 3, 1.1,0,10,0.15,0.5,1)
dis.CFrame = root.CFrame*CFrame.new(0,2,-3)
CreateMesh(dis,"Sphere",10,1,10)
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-5,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(5,0,0)
local trl = Instance.new('Trail',wed)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 0.6
local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
dis.CFrame = dis.CFrame*CFrame.Angles(0,math.rad(rot),0)
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*250
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
local hitted = false
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
    CamShakeAll(20,35,Character)
	CFuncs["EchoSound"].Create("rbxassetid://782200047", dis, 7, 1.1,0,10,0.15,0.5,1)
	MagniDamage(dis, 30, 82,34575, 0, "Normal")
	sphere2(8,"Add",dis.CFrame,vt(10,1,10),1,0.1,1,keptcolor,keptcolor.Color)
	sphere2(4,"Add",dis.CFrame,vt(1,1,1),0.5,0.5,0.5,keptcolor,keptcolor.Color)
	sphere2(3,"Add",dis.CFrame,vt(1,1,1),0.5,0.5,0.5,BrickColor.new("White"),Color3.new(1,1,1))
	coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 10000000
eff.Enabled = true
eff.EmissionDirection = "Front"
eff.Lifetime = NumberRange.new(1)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,75,0),NumberSequenceKeypoint.new(0.1,20,0),NumberSequenceKeypoint.new(0.8,40,0),NumberSequenceKeypoint.new(1,60,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(150)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.2)
eff.Enabled = false
	end))
	coroutine.resume(coroutine.create(function()
		for i = 0, 9 do
local disr = CreateParta(char,1,1,"Neon",keptcolor)
disr.CFrame = dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",disr)
at1.Position = vt(-2,0,0)
local at2 = Instance.new("Attachment",disr)
at2.Position = vt(2,0,0)
local trl = Instance.new('Trail',disr)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://2342682798"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(Color3.new(0.3,1,1))
trl.Lifetime = 0.5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = disr.CFrame.lookVector*math.random(50,200)
bv.Parent = disr
local val = 0
coroutine.resume(coroutine.create(function()
	swait(30)
	for i = 0, 9 do
		swait()
		val = val + 0.1
		trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, val),NumberSequenceKeypoint.new(1, 1)})
	end
game:GetService("Debris"):AddItem(disr, 3)
end))
end
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(Color3.new(0.3,1,1))
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.25)
eff.Enabled = false
end))
	for i = 0, 9 do
		slash(math.random(10,20)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(100,200)/250,BrickColor.new("White"))
	end
for i = 0, 19 do
PixelBlock(1,math.random(5,20),"Add",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),4,4,4,0.08,BrickColor.new("Toothpaste"),0)
end
dis.Anchored = true
dis.Transparency = 1
wait(8)
dis:Destroy()
end
end)
end))
rot = rot - 15
end
for i = 0,2,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(-30),math.rad(0)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(5)),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(60)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10),math.rad(0),math.rad(-50)),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(-20),math.rad(0),math.rad(10)),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(90),math.rad(0),math.rad(-60)),.3)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function RapidBurst()
attack = true
hum.WalkSpeed = 0
CFuncs["Sound"].Create("rbxassetid://1368598393", char, 2.5, 0.5)
CFuncs["Sound"].Create("rbxassetid://1368598393", root, 10, 0.5)
CFuncs["EchoSound"].Create("rbxassetid://1718412034", char, 4, 1,0,10,0.15,0.5,1)
bosschatfunc("S H A T T E R !",MAINRUINCOLOR.Color,2)
local keptcolor = MAINRUINCOLOR
for i = 0,8,0.1 do
swait()
CamShakeAll(15,30,Character)
slash(math.random(25,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,25,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(2,0.01,2),-2,BrickColor.random())
block(10,"Add",root.CFrame*CFrame.new(0,25,0),vt(0,0,0),0.5,0.5,0.5,BrickColor.random(),BrickColor.random().Color)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-15 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.15 + 0.02 * math.cos(sine / 32),-0.1 + 0.05 * math.cos(sine / 32))*angles(math.rad(-15 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-25 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.35,1 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(165 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-10 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.35,1 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(165 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(13 - 3 * math.cos(sine / 45))),.1)
end
CamShakeAll(30,300,Character)
for i = 0, 134 do
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = root.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-25000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(25000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(BrickColor.random().Color)
trl.Lifetime = 12
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
end
symbolizeBlink(root,0,2109052855,MAINRUINCOLOR.Color,125,0,0,0,root,false,0,1)
symbolizeBlink(root,0,2109052855,MAINRUINCOLOR.Color,125,0,0,0,root,false,0,1.5)
symbolizeBlink(root,0,2109052855,MAINRUINCOLOR.Color,125,0,0,0,root,false,0,3)
sphere2(2,"Add",root.CFrame,vt(1,1,1),1,1,1,BrickColor.random(),BrickColor.random().Color)
sphere2(2,"Add",root.CFrame,vt(1,1,1),2,2,2,BrickColor.random(),BrickColor.random().Color)
sphere2(2,"Add",root.CFrame,vt(1,1,1),4,4,4,BrickColor.random(),BrickColor.random().Color)
sphere2(2,"Add",root.CFrame,vt(1,1,1),8,8,8,BrickColor.random(),BrickColor.random().Color)
CFuncs["Sound"].Create("rbxassetid://1841058541", root, 10,1)
CFuncs["Sound"].Create("rbxassetid://2095993595", char, 5,0.8)
CFuncs["Sound"].Create("rbxassetid://1841058541", char, 5,1)
for i = 0, 24 do
slash(math.random(10,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(500,1500)/250,BrickColor.random())
end
local distam = 0
coroutine.resume(coroutine.create(function()
for i = 0, 99 do
	wait()
CamShakeAll(15,100,Character)
distam = distam + 1
local xa = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
xa.Anchored = true
xa.CFrame = root.CFrame*CFrame.new(math.random(-distam,distam),math.random(-distam,distam),math.random(-distam,distam))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
game:GetService("Debris"):AddItem(xa, 5)
for i = 0, 4 do
slash(math.random(25,50)/10,5,true,"Round","Add","Out",xa.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(200,500)/250,BrickColor.random())
end
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",xa)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(xa.Color)
eff.Rate = 10000000
eff.Enabled = true
eff.Lifetime = NumberRange.new(2.5)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,75,0),NumberSequenceKeypoint.new(0.1,20,0),NumberSequenceKeypoint.new(0.8,40,0),NumberSequenceKeypoint.new(1,60,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(200)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.2)
eff.Enabled = false
	end))
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",xa)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.random().Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(1,3)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,30,0),NumberSequenceKeypoint.new(0.2,5,0),NumberSequenceKeypoint.new(0.8,5,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(50,500)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.25)
eff.Enabled = false
end))
CFuncs["Sound"].Create("rbxassetid://675172759", xa, 7,math.random(100,200)/200)
sphere2(5,"Add",xa.CFrame,vt(1,1,1),1,1,1,BrickColor.random(),BrickColor.random().Color)
sphere2(5,"Add",xa.CFrame,vt(1,1,1),2,2,2,BrickColor.random(),BrickColor.random().Color)
MagniDamage(xa, 60, 9999,99999, 0, "Normal")
end
end))
attack = false
hum.WalkSpeed = storehumanoidWS
end

function DivineSwarm()
attack = true
hum.WalkSpeed = 2
local keptcolor = MAINRUINCOLOR
CFuncs["EchoSound"].Create("rbxassetid://1535994669", char, 1.5, 1,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://1535994669", root, 60, 1,0,10,0.15,0.5,1)
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("Divine Swarm.",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("I'll take you out again.",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("Give up to justice.",MAINRUINCOLOR.Color,1)
end
coroutine.resume(coroutine.create(function()
for i = 0, 12 do
swait(8)
local dis = CreateParta(char,0.5,1,"Neon",MAINRUINCOLOR)
dis.Anchored = true
CFuncs["Sound"].Create("rbxassetid://137463716", dis, 2.5,1.5)
dis.CFrame = root.CFrame*CFrame.new(math.random(-35,35),math.random(10,45),math.random(-35,35))
CreateMesh(dis,"Sphere",2,2,2)
sphere2(5,"Add",dis.CFrame,vt(1,1,1),0.1,0.1,0.1,keptcolor,keptcolor.Color)
slash(math.random(10,20)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(10,50)/250,BrickColor.new("White"))
coroutine.resume(coroutine.create(function()
wait(0.75)
dis.Anchored = false
CFuncs["EchoSound"].Create("rbxassetid://1602800656", dis, 9, 1,0,2,0.15,0.1,1)
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-1,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(1,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 0.6
local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*2000
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
local hitted = false
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
	CFuncs["EchoSound"].Create("rbxassetid://675172759", dis, 5, 0.8,0,10,0.15,0.5,1)
	CFuncs["EchoSound"].Create("rbxassetid://1448044156", dis, 6, 0.8,0,10,0.15,0.5,1)
	MagniDamage(dis, 60, 15,35, 0, "Normal")
	sphere2(1,"Add",dis.CFrame,vt(1,1,1),1,1,1,keptcolor,keptcolor.Color)
	sphere2(8,"Add",dis.CFrame,vt(1,1,1),1.25,1.25,1.25,BrickColor.new("White"),Color3.new(1,1,1))
	coroutine.resume(coroutine.create(function()
for i = 0, 2 do
local disr = CreateParta(char,1,1,"Neon",keptcolor)
disr.CFrame = dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",disr)
at1.Position = vt(-10,0,0)
local at2 = Instance.new("Attachment",disr)
at2.Position = vt(10,0,0)
local trl = Instance.new('Trail',disr)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://2342682798"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(disr.Color)
trl.Lifetime = 0.5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = disr.CFrame.lookVector*math.random(125,250)
bv.Parent = disr
local val = 0
coroutine.resume(coroutine.create(function()
	swait(30)
	for i = 0, 9 do
		swait()
		val = val + 0.1
		trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, val),NumberSequenceKeypoint.new(1, 1)})
	end
game:GetService("Debris"):AddItem(disr, 3)
end))
end
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://1049219073"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.5)
eff.Enabled = false
end))
	for i = 0, 4 do
		slash(math.random(20,50)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(100,200)/250,BrickColor.new("White"))
	end
coroutine.resume(coroutine.create(function()
for i = 0, 19 do
swait()
CamShakeAll(5,30,Character)
end
end))
dis.Anchored = true
dis.Transparency = 1
wait(8)
dis:Destroy()
end
end)
end))
end))
end
end))
for i = 0,2,0.1 do
swait()
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,MAINRUINCOLOR,MAINRUINCOLOR.Color)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),-0.1 + 0.05 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-15 - 2 * math.cos(sine / 37)),math.rad(-15 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(170 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 45))),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(8 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-9 - 4 * math.cos(sine / 45))),.3)
end
for i = 0,11,0.1 do
swait()
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,MAINRUINCOLOR,MAINRUINCOLOR.Color)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),-0.1 + 0.05 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-75)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(15 - 2 * math.cos(sine / 37)),math.rad(-15 + 1 * math.cos(sine / 58)),math.rad(75 + 2 * math.cos(sine / 53))),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(9 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(9 + 2 * math.cos(sine / 45))),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(90 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-75 - 4 * math.cos(sine / 45))),.3)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function FallenOrbs()
attack = true
hum.WalkSpeed = 2
local keptcolor = MAINRUINCOLOR
CFuncs["EchoSound"].Create("rbxassetid://1448033299", char, 1.5, 1,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://1448033299", root, 10, 1,0,10,0.15,0.5,1)
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("This wont be easy to you.",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("How about this?",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("How do you like this?",MAINRUINCOLOR.Color,1)
end
local obj1 = script.chring:Clone()
obj1.Parent = char
obj1.Transparency = 1
obj1.Color = BrickColor.new("Toothpaste").Color
local obj2 = script.spball:Clone()
obj2.Parent = char
obj2.Transparency = 1
obj2.Color = MAINRUINCOLOR.Color
local cfor = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
cfor.Anchored = true
cfor.CFrame = obj2.CFrame
local cef = Instance.new("ParticleEmitter",cfor)
cef.Texture = "rbxassetid://2344870656"
cef.LightEmission = 1
cef.Color = ColorSequence.new(obj2.Color)
cef.Rate = 150
cef.Lifetime = NumberRange.new(0.25)
cef.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.5,1,0),NumberSequenceKeypoint.new(1,0,0)})
cef.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.5,0.25,0),NumberSequenceKeypoint.new(1,1,0)})
cef.Speed = NumberRange.new(0)
local rval = 0
local eval = 1
CFuncs["Sound"].Create("rbxassetid://136007472", root, 10,0.7)
for i = 0,10,0.1 do
swait()
rval = rval + math.random(30,40)
eval = eval + 1.5
obj1.Transparency = obj1.Transparency - 0.003
obj1.Size = obj1.Size + vt(0,1,1)
obj1.CFrame = root.CFrame*CFrame.new(0,16,0)*CFrame.Angles(math.rad(0),math.rad(rval),math.rad(-90))
obj2.Transparency = obj2.Transparency - 0.005
obj2.Size = obj2.Size + vt(0.5,0.5,0.5)
cef.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.5,eval,0),NumberSequenceKeypoint.new(1,0,0)})
obj2.CFrame = root.CFrame*CFrame.new(0,36,0)*CFrame.Angles(math.rad(rval),math.rad(rval),math.rad(-rval))
cfor.CFrame = obj2.CFrame
slash(math.random(50,90)/10,5,true,"Round","Add","In",obj2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,0.01,1),math.random(-400,-200)/250,BrickColor.new("Deep orange"))
slash(math.random(50,90)/10,5,true,"Round","Add","In",obj2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,0.01,1),math.random(-400,-200)/250,BrickColor.new("Toothpaste"))
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,MAINRUINCOLOR,MAINRUINCOLOR.Color)
sphere2(8,"Add",rleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Deep orange"),BrickColor.new("Deep orange").Color)
sphere2(8,"Add",lleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Toothpaste"),BrickColor.new("Toothpaste").Color)

RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 32))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 2 * math.cos(sine / 32))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 43),0 - 0.25 * math.cos(sine / 53),6 + 1 * math.cos(sine / 32))*angles(math.rad(-20 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(70)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-5 - 2 * math.cos(sine / 37)),math.rad(5 + 1 * math.cos(sine / 58)),math.rad(-70 + 2 * math.cos(sine / 53))),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(170 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(10 + 2 * math.cos(sine / 45))),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(8 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-9 - 4 * math.cos(sine / 45))),.3)
end
CamShakeAll(40,120,Character)
cef.Enabled = false
coroutine.resume(coroutine.create(function()
	for i = 0,49 do
		swait()
		rval = rval + 100
		obj2.CFrame = obj2.CFrame*CFrame.Angles(math.rad(rval),math.rad(rval),math.rad(-rval))
		obj2.Transparency = obj2.Transparency + 0.02
		obj2.Size = obj2.Size + vt(5,5,5)
		obj1.Transparency = obj1.Transparency + 0.02
		obj1.Size = obj1.Size + vt(0,-0.5,-0.5)
	end
	obj1:Destroy()
	obj2:Destroy()
	cfor:Destroy()
end))
for i = 0, 9 do
slash(math.random(10,40)/10,5,true,"Round","Add","Out",obj2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(100,450)/250,BrickColor.new("Deep orange"))
slash(math.random(10,40)/10,5,true,"Round","Add","Out",obj2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(100,450)/250,BrickColor.new("Toothpaste"))
end
sphere2(3,"Add",obj2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),1,1,1,MAINRUINCOLOR,MAINRUINCOLOR.Color)
sphere2(3,"Add",obj2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),2,2,2,MAINRUINCOLOR,MAINRUINCOLOR.Color)
CFuncs["EchoSound"].Create("rbxassetid://675172759", root, 10, 0.8,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://763717897", root, 7.5, 1.1,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://675172759", root, 5, 0.7,0,10,0.15,0.5,1)
CamShakeAll(40,100,Character)
coroutine.resume(coroutine.create(function()
for i = 0, 26 do
	swait()
	local custcol = math.random(1,3)
local dis = CreateParta(char,0.5,1,"Neon",MAINRUINCOLOR)
if custcol == 1 then
dis.BrickColor = MAINRUINCOLOR
elseif custcol == 2 then
dis.BrickColor = BrickColor.new("Toothpaste")
elseif custcol == 3 then
dis.BrickColor = BrickColor.new("Deep orange")
end
dis.Anchored = true
CFuncs["Sound"].Create("rbxassetid://137463716", dis, 2.5,1.5)
dis.CFrame = root.CFrame*CFrame.new(math.random(-30,30),math.random(11,51),math.random(-30,30))
CreateMesh(dis,"Sphere",2,2,2)
sphere2(5,"Add",dis.CFrame,vt(1,1,1),0.1,0.1,0.1,dis.BrickColor,dis.Color)
slash(math.random(10,20)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(10,50)/250,BrickColor.new("White"))
coroutine.resume(coroutine.create(function()
wait(1)
dis.Anchored = false
CFuncs["EchoSound"].Create("rbxassetid://1602800656", dis, 5, 1,0,2,0.15,0.1,1)
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-1,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(1,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 0.6
local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*650
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 5)
local hitted = false
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
CamShakeAll(20,50,Character)
	CFuncs["EchoSound"].Create("rbxassetid://675172759", dis, 2.5, 0.8,0,10,0.15,0.5,1)
	MagniDamage(dis, 60, 25456,124672, 0, "Normal")
	sphere2(1,"Add",dis.CFrame,vt(1,1,1),1,1,1,dis.BrickColor,dis.Color)
	sphere2(8,"Add",dis.CFrame,vt(1,1,1),1.25,1.25,1.25,BrickColor.new("White"),Color3.new(1,1,1))
	coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 10000000
eff.Enabled = true
eff.EmissionDirection = "Front"
eff.Lifetime = NumberRange.new(3)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,75,0),NumberSequenceKeypoint.new(0.1,20,0),NumberSequenceKeypoint.new(0.8,40,0),NumberSequenceKeypoint.new(1,60,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.2)
eff.Enabled = false
	end))
	coroutine.resume(coroutine.create(function()
for i = 0, 4 do
local disr = CreateParta(char,1,1,"Neon",dis.BrickColor)
disr.CFrame = dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",disr)
at1.Position = vt(-10,0,0)
local at2 = Instance.new("Attachment",disr)
at2.Position = vt(10,0,0)
local trl = Instance.new('Trail',disr)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://2342682798"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(disr.Color)
trl.Lifetime = 0.5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = disr.CFrame.lookVector*math.random(125,250)
bv.Parent = disr
local val = 0
coroutine.resume(coroutine.create(function()
	swait(30)
	for i = 0, 9 do
		swait()
		val = val + 0.1
		trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, val),NumberSequenceKeypoint.new(1, 1)})
	end
game:GetService("Debris"):AddItem(disr, 3)
end))
end
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.5)
eff.Enabled = false
end))
	for i = 0, 4 do
		slash(math.random(20,50)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(100,200)/250,BrickColor.new("White"))
	end
dis.Anchored = true
dis.Transparency = 1
wait(8)
dis:Destroy()
end
end)
end))
end))
end
end))
for i = 0,9,0.1 do
swait()
sphere2(8,"Add",rleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Deep orange"),BrickColor.new("Deep orange").Color)
sphere2(8,"Add",lleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Toothpaste"),BrickColor.new("Toothpaste").Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 32))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 2 * math.cos(sine / 32))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 43),0 - 0.25 * math.cos(sine / 53),6 + 1 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(90)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(15 - 2 * math.cos(sine / 37)),math.rad(-15 + 1 * math.cos(sine / 58)),math.rad(-90 + 2 * math.cos(sine / 53))),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(90 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(90 + 2 * math.cos(sine / 45))),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(8 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-9 - 4 * math.cos(sine / 45))),.3)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end



function EquinoxOrbs()
hum.WalkSpeed = 0
attack = true
for i = 0,1,0.1 do
swait()
	RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20)),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20)),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.5,0.5)*angles(math.rad(90),math.rad(0),math.rad(0)),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17),math.rad(0),math.rad(0)),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(0),math.rad(5),math.rad(40)),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(0),math.rad(-5),math.rad(-40)),.3)
end
sphere2(5,"Add",root.CFrame,vt(1,1,1),1.5,1.5,1.5,MAINRUINCOLOR)
sphere2(5,"Add",root.CFrame,vt(1,1,1),1,1,1,MAINRUINCOLOR)
for i = 0, 24 do
		slash(math.random(10,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(90),math.rad(math.random(-360,360)),math.rad(0)),vt(0.01,0.01,0.01),math.random(100,400)/250,BrickColor.new("White"))
end
CFuncs["Sound"].Create("rbxassetid://763716870", root, 8,1)
CFuncs["Sound"].Create("rbxassetid://782353443", root, 10,0.8)
CFuncs["Sound"].Create("rbxassetid://782225570", root, 9,0.5)
CFuncs["Sound"].Create("rbxassetid://763717569", root, 8,0.9)
for i = 0,4,0.1 do
swait()
root.CFrame = root.CFrame + root.CFrame.lookVector*7.5
local dis = CreateParta(char,0.25,1,"Neon",MAINRUINCOLOR)
CreateMesh(dis,"Sphere",1,1,1)
dis.Anchored = true
dis.CFrame = larm.CFrame*CFrame.new(0,-3,0)
local dis2 = CreateParta(char,0.25,1,"Neon",BrickColor.new("White"))
CreateMesh(dis2,"Sphere",1,1,1)
dis2.Anchored = true
dis2.CFrame = rarm.CFrame*CFrame.new(0,-3,0)
sphere2(5,"Add",dis.CFrame,vt(1,1,1),0.1,0.1,0.1,dis.BrickColor,dis.Color)
sphere2(5,"Add",dis2.CFrame,vt(1,1,1),0.1,0.1,0.1,dis2.BrickColor,dis2.Color)
coroutine.resume(coroutine.create(function()
	swait(60)
	dis.Transparency = 1
	dis2.Transparency = 1
coroutine.resume(coroutine.create(function()
for i = 0, 19 do
swait()
CamShakeAll(10,30,Character)
end
end))
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(50,450)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eff2 = eff:Clone()
eff2.Parent = dis2
eff2.LightEmission = 0
eff2.Color = ColorSequence.new(dis2.Color)
wait(0.25)
eff.Enabled = false
eff2.Enabled = false
end))
MagniDamage(dis, 55, 89,219788936, 0, "Normal")
MagniDamage(dis2, 55, 89,219788936, 0, "Normal")
    for i = 0, 2 do
		slash(math.random(10,80)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(100,300)/250,dis.BrickColor)
		slash(math.random(10,80)/10,5,true,"Round","Add","Out",dis2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(100,300)/250,dis2.BrickColor)
	end
	CFuncs["Sound"].Create("rbxassetid://782353117", dis, 1,0.75)
	CFuncs["Sound"].Create("rbxassetid://782353117", dis2, 1,0.75)
	CFuncs["Sound"].Create("rbxassetid://1666361078", dis, 1,1.25)
	CFuncs["Sound"].Create("rbxassetid://1666361078", dis2, 1,1.25)
	CFuncs["Sound"].Create("rbxassetid://782353443", dis, 2,1.15)
	CFuncs["Sound"].Create("rbxassetid://782353443", dis2, 2,1.15)
	sphere2(3,"Add",dis.CFrame,vt(1,1,1),0.8,0.8,0.8,dis.BrickColor,dis.Color)
	sphere2(3,"Add",dis2.CFrame,vt(1,1,1),0.8,0.8,0.8,dis2.BrickColor,dis2.Color)
end))
game:GetService("Debris"):AddItem(dis, 5)
game:GetService("Debris"):AddItem(dis2, 5)
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20)),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20)),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.5,0.5)*angles(math.rad(90),math.rad(0),math.rad(0)),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17),math.rad(0),math.rad(0)),.2)
RW.C0=clerp(RW.C0,cf(1.4,1.5,0)*angles(math.rad(0),math.rad(5),math.rad(210)),.1)
LW.C0=clerp(LW.C0,cf(-1.4,1.5,0)*angles(math.rad(0),math.rad(-5),math.rad(-210)),.1)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end
function FallenDEMISE()
attack = true
hum.WalkSpeed = 0
local keptcolor = MAINRUINCOLOR
bosschatfunc("ALL OF YOUR EXISTANCE WILL BE GONE.",MAINRUINCOLOR.Color,3)
CFuncs["Sound"].Create("rbxassetid://289315275", char, 2.5,0.75)
CFuncs["Sound"].Create("rbxassetid://136007472", char, 2,0.5)
for i = 0, 15, 0.1 do
swait()
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = root.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-25000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(25000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 1)
sphere2(15,"Add",root.CFrame,vt(8,8,8),2,2,2,MAINRUINCOLOR)
slash(math.random(30,150)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(1,0.01,1),math.random(100,500)/250,BrickColor.new("Toothpaste"))
slash(math.random(30,150)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(1,0.01,1),math.random(100,500)/250,BrickColor.new("Deep orange"))
RH.C0=clerp(RH.C0,cf(1,-0.35,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(-35)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.45,-0.5)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(35)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(5),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(1.15,0.5,-0.5)*angles(math.rad(92),math.rad(0),math.rad(-67)),.1)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(68)),.1)
end
CFuncs["Sound"].Create("rbxassetid://294188875", char, 10,1)
for i = 0, 30, 0.1 do
swait()
CamShakeAll(10,350,Character)
coroutine.resume(coroutine.create(function()
for i, v in pairs(FindNearestHead(root.CFrame.p, 10000000)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
end))
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = root.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-50000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(50000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 10
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(1500,10000)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, math.random(1,4))
sphere2(15,"Add",root.CFrame,vt(8,80000,8),5,1,5,MAINRUINCOLOR)
sphere2(15,"Add",root.CFrame,vt(8,8,8),8,8,8,MAINRUINCOLOR)
sphere2(2,"Add",root.CFrame*CFrame.new(math.random(-2000,2000),math.random(-2000,2000),math.random(-2000,2000)),vt(0,0,0),5,5,5,BrickColor.new("Deep orange"))
sphere2(2,"Add",root.CFrame*CFrame.new(math.random(-2000,2000),math.random(-2000,2000),math.random(-2000,2000)),vt(0,0,0),5,5,5,BrickColor.new("Toothpaste"))
slash(math.random(50,100)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(5,0.01,5),math.random(500,5000)/250,BrickColor.new("Deep orange"))
slash(math.random(50,100)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(5,0.01,5),math.random(500,5000)/250,BrickColor.new("Toothpaste"))
for i = 0, 2 do
slash(math.random(50,100)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,math.random(-3,1000),0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(2,0.01,2),math.random(250,750)/250,MAINRUINCOLOR)
end
RH.C0=clerp(RH.C0,cf(1,-0.35,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(-35)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.45,-0.5)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(0),math.rad(35)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0)*angles(math.rad(5),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(1.15,0.5,-0.5)*angles(math.rad(92),math.rad(0),math.rad(-67)),.1)
LW.C0=clerp(LW.C0,cf(-1.15,0.5,-0.5)*angles(math.rad(90),math.rad(0),math.rad(68)),.1)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function SHDTwist()
	attack = true
hum.WalkSpeed = 2
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("Plasmatic Burst!",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("How cute.",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("Suffer to the brightness.",MAINRUINCOLOR.Color,1)
end
CFuncs["Sound"].Create("rbxassetid://136007472", rarm, 1.5,1.25)
local obj1 = script.chring2:Clone()
obj1.Parent = char
obj1.Transparency = 1
obj1.Size = vt(1,1,1)
obj1.Color = BrickColor.new("Pink").Color
local obj2 = script.spball:Clone()
obj2.Parent = char
obj2.Transparency = 1
obj2.Size = vt(1,1,1)
obj2.Color = MAINRUINCOLOR.Color
local cfor = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
cfor.Anchored = true
cfor.CFrame = obj2.CFrame
local cef = Instance.new("ParticleEmitter",cfor)
cef.Texture = "rbxassetid://2344870656"
cef.LightEmission = 1
cef.Color = ColorSequence.new(obj2.Color)
cef.Rate = 150
cef.Lifetime = NumberRange.new(0.25)
cef.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.5,1,0),NumberSequenceKeypoint.new(1,0,0)})
cef.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.5,0.25,0),NumberSequenceKeypoint.new(1,1,0)})
cef.Speed = NumberRange.new(0)
local rval = 0
local eval = 1
	for i = 0,7,0.1 do
swait()
rval = rval + math.random(30,40)
eval = eval + 0.45
obj1.Transparency = obj1.Transparency - 0.005
obj1.Size = obj1.Size + vt(0.3,0.3,0.1)
obj1.CFrame = root.CFrame*CFrame.new(0,1,-5)*CFrame.Angles(math.rad(0),math.rad(0),math.rad(rval))
obj2.Transparency = obj2.Transparency - 0.007
obj2.Size = obj2.Size + vt(0.15,0.15,0.15)
cef.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.5,eval,0),NumberSequenceKeypoint.new(1,0,0)})
obj2.CFrame = root.CFrame*CFrame.new(0,1,-7)*CFrame.Angles(math.rad(rval),math.rad(rval),math.rad(-rval))
cfor.CFrame = obj2.CFrame
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Pastel light blue"),BrickColor.new("Pastel light blue").Color)
sphere2(10,"Add",larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.15,-0.01,BrickColor.new("Pink"),BrickColor.new("Pink").Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 32))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 2 * math.cos(sine / 32))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-50)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 2 * math.cos(sine / 37)),math.rad(10 + 1 * math.cos(sine / 58)),math.rad(50 + 2 * math.cos(sine / 53))),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(10 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(5 + 2 * math.cos(sine / 45))),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(90 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-50 - 4 * math.cos(sine / 45))),.3)
	end
	cef.Enabled = false
	coroutine.resume(coroutine.create(function()
	for i = 0,49 do
		swait()
		rval = rval + 100
		obj2.CFrame = obj2.CFrame*CFrame.Angles(math.rad(rval),math.rad(rval),math.rad(-rval))
		obj2.Transparency = obj2.Transparency + 0.02
		obj2.Size = obj2.Size + vt(5,5,5)
		obj1.Transparency = obj1.Transparency + 0.02
		obj1.Size = obj1.Size + vt(0,-0.5,-0.5)
	end
	obj1:Destroy()
	obj2:Destroy()
	cfor:Destroy()
end))
	local lva = 1
	local ica = 0
local cent = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
CFuncs["Sound"].Create("rbxassetid://1177785010", cent, 10, 1)
cent.CFrame = root.CFrame*CFrame.Angles(0,0,0) + root.CFrame.lookVector*5
sphere2(2,"Add",cent.CFrame,vt(1,1,1),0.5,0.5,0.5,BrickColor.new("Pastel light blue"),BrickColor.new("Pastel light blue").Color)
sphere2(3,"Add",cent.CFrame,vt(1,1,1),0.5,0.5,0.5,BrickColor.new("Pink"),BrickColor.new("Pink").Color)

local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    cent.CFrame.p,                           -- origin
	    (mouse.Hit.p - cent.CFrame.p).unit * 500 -- direction
	) 
	local ignore = cent
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (cent.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(cent.CFrame.p, position) * CFrame.new(0, 0, 0)
cent.CFrame = a.CFrame
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = cent.CFrame.lookVector*0
bv.Parent = cent
game:GetService("Debris"):AddItem(cent, 20)
local hitted = false
coroutine.resume(coroutine.create(function()
	while true do
		swait(1)
		if hitted == false and cent.Parent ~= nil then
	ica = ica + 4*lva
	lva = lva + 0.1
	bv.velocity = cent.CFrame.lookVector*ica
	sphere2(3,"Add",cent.CFrame,vt(5,5,5),-0.05,-0.05,-0.05,BrickColor.new("Pastel light blue"))
	sphere2(5,"Add",cent.CFrame*CFrame.Angles(0,0,math.rad(ica))*CFrame.new(0,-5,0),vt(4,4,4),-0.04,-0.04,-0.04,BrickColor.new("Pink"))
	sphere2(5,"Add",cent.CFrame*CFrame.Angles(0,0,math.rad(ica))*CFrame.new(0,5,0),vt(4,4,4),-0.04,-0.04,-0.04,BrickColor.new("Pastel light blue"))
		elseif hitted == true or cent.Parent == nil then
			break
		end
	end
end))
coroutine.resume(coroutine.create(function()
cent.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
	cent.Anchored = true
	CFuncs["Sound"].Create("rbxassetid://782353443", cent, 10, 1)
	CFuncs["Sound"].Create("rbxassetid://1368637781", cent, 8, 1)
	CFuncs["Sound"].Create("rbxassetid://763717897", cent, 5, 1)
	CFuncs["EchoSound"].Create("rbxassetid://1177785010", cent, 8, 1.1,0,10,0.15,0.5,1)
	MagniDamage(cent, 50, 50,99999, 0, "Normal")
	sphere2(2,"Add",cent.CFrame,vt(1,1,1),1,1,1,BrickColor.new("Pastel light blue"),BrickColor.new("Pastel light blue").Color)
	sphere2(3,"Add",cent.CFrame,vt(1,1,1),1.2,1.2,1.2,BrickColor.new("Pink"),BrickColor.new("Pink").Color)
	for i = 0, 19 do
		slash(math.random(10,50)/10,5,true,"Round","Add","Out",cent.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(200,400)/250,BrickColor.new("Pink"))
		slash(math.random(10,50)/10,5,true,"Round","Add","Out",cent.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(150,300)/250,BrickColor.new("Pastel light blue"))
	end
	coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",cent)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.new("Pastel light blue").Color)
eff.Rate = 10000000
eff.Enabled = true
eff.EmissionDirection = "Front"
eff.Lifetime = NumberRange.new(5)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,75,0),NumberSequenceKeypoint.new(0.1,40,0),NumberSequenceKeypoint.new(0.8,60,0),NumberSequenceKeypoint.new(1,80,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(350)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
local eff2 = eff:Clone()
eff2.Parent = cent
eff2.Speed = NumberRange.new(250) 
eff2.Color = ColorSequence.new(BrickColor.new("Pink").Color)
wait(0.2)
eff.Enabled = false
eff2.Enabled = false
	end))
end
end)
end))
attack = false
hum.WalkSpeed = storehumanoidWS
end

function CardStorm()
attack = true
hum.WalkSpeed = 0 
local keptcolor = MAINRUINCOLOR
	for i = 0,2,0.1 do
		swait()
sphere2(8,"Add",LeftArm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("White"),BrickColor.new("Deep orange").Color)
            RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),7 + 1 * math.cos(sine / 32))* angles(math.rad(0),math.rad(0),math.rad(40)),0.3)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(5),math.rad(0),math.rad(-40)),.3)
RW.C0 = clerp(RW.C0, CFrame.new(1.5, 0.5, 0) * angles(math.rad(6), math.rad(-20), math.rad(12)), 0.3)
LW.C0 = clerp(LW.C0, CFrame.new(-1.5, 0.5, 0) * angles(math.rad(50), math.rad(40), math.rad(-40)), 0.3)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
	end
local RCARD = {83485107,83486447,83485303,83483451,83486567,83484022,83486756}

local orb = Instance.new("Part", char)
        orb.BrickColor = keptcolor
        orb.CanCollide = false
        orb.FormFactor = 3
        orb.Name = "Ring"
        orb.Material = "Neon"
        orb.Size = Vector3.new(1, 0.75, 1.25)
        orb.Transparency = 0
        orb.TopSurface = 0
        orb.BottomSurface = 0
        local orbm = Instance.new("SpecialMesh", orb)
        orbm.MeshType = "Brick"
orbm.Name = "SizeMesh"
orbm.Scale = vt(4,4,4)
orb.CFrame = root.CFrame*CFrame.new(0,8,-3) + root.CFrame.lookVector*3
local eff = Instance.new("ParticleEmitter",orb)
coroutine.resume(coroutine.create(function()
	while true do
	swait()
	eff.Texture = "rbxassetid://"..RCARD[math.random(1,7)]
	end
end))
eff.LightEmission = 0.95
eff.Color = ColorSequence.new(Color3.new(1,1,1))
eff.Rate = 10000
eff.Lifetime = NumberRange.new(1.5)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,2.75,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.75,0.5,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,85)
eff.Drag = 5
eff.Rotation = NumberRange.new(-250,250)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
	local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    orb.CFrame.p,                           -- origin
	    (mouse.Hit.p - orb.CFrame.p).unit * 500 -- direction
	) 
	local ignore = orb
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (orb.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, 0)
orb.CFrame = a.CFrame
a:Destroy()
CFuncs["Sound"].Create("rbxassetid://304448425", orb, 1.5, 0.9)
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = orb.CFrame.lookVector*300
bv.Parent = orb
game:GetService("Debris"):AddItem(orb, 10)
local hitted = false
coroutine.resume(coroutine.create(function()
while true do
swait()
if orb.Parent ~= nil and hitted == false then
sphere2(4,"Add",orb.CFrame*CFrame.new(math.random(-1,1),math.random(-1,1),0) - orb.CFrame.lookVector*1.5,vt(3,3,3),-0.03,-0.03,-0.03,keptcolor)
elseif orb.Parent == nil and hitted == true then
break
end
end
end))
local hit =orb.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
	eff.Enabled = false
CamShakeAll(30,50,Character)
CFuncs["Sound"].Create("rbxassetid://1226980789", orb, 4.5, 0.7)
CFuncs["Sound"].Create("rbxassetid://178452221", orb, 2.5, 0.4)
	MagniDamage(orb, 25*2, 20,30, 0, "Normal")
sphere2(4,"Add",orb.CFrame,vt(4*2,4*2,4*2),0.5*2,0.5*2,0.5*2,keptcolor)
sphere2(3,"Add",orb.CFrame,vt(4*2,4*2,4*2),0.5*2,0.5*2,0.5*2,keptcolor)
sphere2(2,"Add",orb.CFrame,vt(4*2,4*2,4*2),0.5*2,0.5*2,0.5*2,keptcolor)
for i = 0, 9 do
sphere2(4,"Add",orb.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1.5,1,1.5),-0.005,4,-0.005,keptcolor)
end
for i = 0, 49 do
local rsiz = math.random(10,30)
sphereMK(math.random(1,3),1,"Add",orb.CFrame*CFrame.new(math.random(-20,20)/50,math.random(-20,20)/50,math.random(-20,20)/50)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),rsiz/10,rsiz/10,rsiz/10,0,keptcolor,0)
end
local eff = Instance.new("ParticleEmitter",orb)
coroutine.resume(coroutine.create(function()
	while true do
	swait()
	eff.Texture = "rbxassetid://"..RCARD[math.random(1,7)]
	end
end))
eff.LightEmission = 0.95
eff.Color = ColorSequence.new(Color3.new(1,1,1))
eff.Rate = 10000
eff.Lifetime = NumberRange.new(1.5)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.1,15,0),NumberSequenceKeypoint.new(0.8,25,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.8,0.5,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(150,350)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-500,500)
coroutine.resume(coroutine.create(function()
	wait(0.25)
	eff.Enabled = false
end))
local hfr,pfr=rayCast(orb.Position,(CFrame.new(orb.Position,orb.Position - Vector3.new(0,1,0))).lookVector,4,char)
orb.Anchored = true
orb.Transparency = 1
coroutine.resume(coroutine.create(function()
if hfr ~= nil then
orb.Size = vt(50,1,50)
orb.Orientation = vt(0,0,0)
orb.CFrame = orb.CFrame*CFrame.new(0,-5,0)
orbm:Destroy()
local firef = eff:Clone()
coroutine.resume(coroutine.create(function()
	while true do
	swait()
	firef.Texture = "rbxassetid://"..RCARD[math.random(1,7)]
	end
end))
firef.Parent = orb
firef.VelocitySpread = 50
firef.Rate = 500
firef.Drag = 3
firef.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.2,0.5,0),NumberSequenceKeypoint.new(1,1,0)})
firef.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,1.5,0),NumberSequenceKeypoint.new(0.05,2.5,0),NumberSequenceKeypoint.new(0.4,1,0),NumberSequenceKeypoint.new(1,0,0)})
firef.Speed = NumberRange.new(10,30)
for i = 0, 24 do
wait(0.25)
MagniDamage(orb, 25, 3,6, 0, "Normal")
end
firef.Enabled = false
end
end))
wait(10)
orb:Destroy()
end
end)
	for i = 0,1,0.1 do
		swait()
sphere2(8,"Add",LeftArm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("White"),BrickColor.new("Deep orange").Color)
            RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),7 + 1 * math.cos(sine / 32))* angles(math.rad(-15),math.rad(0),math.rad(-50)),0.5)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(5),math.rad(0),math.rad(50)),.5)
RW.C0 = clerp(RW.C0, CFrame.new(1.5, 0.5, 0) * angles(math.rad(6), math.rad(-20), math.rad(12)), 0.5)
LW.C0 = clerp(LW.C0, CFrame.new(-1.5, 0.5, 0) * angles(math.rad(140), math.rad(0), math.rad(-20)), 0.5)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
	end
hum.WalkSpeed = storehumanoidWS
attack = false
end

function hugg()
attack = true
hum.WalkSpeed = 5
local rsiz = math.random(5,15)
for i = 0, 2, 0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(7.5),math.rad(0),math.rad(-10 + 1 * math.cos(sine / 34))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(2.5),math.rad(0),math.rad(0 + 1 * math.cos(sine / 34))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 2.5 * math.cos(sine / 28)),math.rad(10),math.rad(0)),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0)*angles(math.rad(110),math.rad(0),math.rad(40)),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(110),math.rad(0),math.rad(-40)),.3)
end
CFuncs["Sound"].Create("rbxassetid://444895479", root, 0.5,1)
	local hb = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
	hb.Anchored = true
	hb.CFrame = root.CFrame*CFrame.new(0,1,0) + root.CFrame.lookVector*2
local huggedperson = nil
local act = false
local alreadydid = false
for i = 0, 3, 0.1 do
swait()
if act == false then
for i, v in pairs(FindNearestHead(hb.CFrame.p, 1.25)) do
if v:FindFirstChild('Head') then
if alreadydid == false then
huggedperson = v
hum.WalkSpeed = 0
CFuncs["Sound"].Create("rbxassetid://294861193", root, 1,1)
CFuncs["Sound"].Create("rbxassetid://200632821", root, 1,1.15)
CFuncs["Sound"].Create("rbxassetid://1042716828", root, 1.5,1)
CFuncs["Sound"].Create("rbxassetid://884155627", root, 2,1)
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("I'm sorry.. if I hurt you..",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("I..I'm interested in you",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("Don't worry.. I'm with you",MAINRUINCOLOR.Color,1)
end
root.Anchored = true
coroutine.resume(coroutine.create(function()
huggedperson.Humanoid.WalkSpeed = 0
huggedperson:WaitForChild("HumanoidRootPart").Anchored = true
end))
chatfunc(string.lower(huggedperson.Name).. "~~",BrickColor.new("Pink").Color,"Normal","SourceSansBold",1)
alreadydid = true
act = true
print(huggedperson.Name)
end
end
end
hb.CFrame = root.CFrame*CFrame.new(0,1,0) + root.CFrame.lookVector*2
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 2.5 * math.cos(sine / 28)),math.rad(10),math.rad(0)),.3)
elseif act == true then
coroutine.resume(coroutine.create(function()
huggedperson:FindFirstChildOfClass("Humanoid").Health = huggedperson:FindFirstChildOfClass("Humanoid").Health + 0.2
end))
rsiz = math.random(5,15)
sphereMK(math.random(1,4),0.15,"Add",root.CFrame*CFrame.new(math.random(-5,5),math.random(-8,-4),math.random(-5,5))*CFrame.Angles(math.rad(90),0,0),rsiz/20,rsiz/20,rsiz/20,0,MAINRUINCOLOR,0)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 2.5 * math.cos(sine / 28)),math.rad(10),math.rad(40)),.1)
huggedperson.Head.CFrame = root.CFrame*CFrame.new(0,1.25,-1.25)*CFrame.Angles(0,math.rad(180),0)
end
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(7.5),math.rad(0),math.rad(-10 + 1 * math.cos(sine / 34))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(2.5),math.rad(0),math.rad(0 + 1 * math.cos(sine / 34))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
RW.C0=clerp(RW.C0,cf(1,0.7,-1)*angles(math.rad(120),math.rad(0),math.rad(-50)),.3)
LW.C0=clerp(LW.C0,cf(-1,0.7,-1)*angles(math.rad(100),math.rad(0),math.rad(50)),.3)
end
hb:Destroy()
if act == true then
for x = 0, 2 do
for i = 0, 2, 0.1 do
swait()
coroutine.resume(coroutine.create(function()
huggedperson:FindFirstChildOfClass("Humanoid").Health = huggedperson:FindFirstChildOfClass("Humanoid").Health + 0.2
end))
rsiz = math.random(5,15)
sphereMK(math.random(1,4),0.15,"Add",root.CFrame*CFrame.new(math.random(-5,5),math.random(-8,-4),math.random(-5,5))*CFrame.Angles(math.rad(90),0,0),rsiz/20,rsiz/20,rsiz/20,0,MAINRUINCOLOR,0)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 2.5 * math.cos(sine / 28)),math.rad(10),math.rad(45)),.1)
huggedperson.Head.CFrame = root.CFrame*CFrame.new(0,1.25,-1.25)*CFrame.Angles(0,math.rad(180),0)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(7.5),math.rad(0),math.rad(-10 + 1 * math.cos(sine / 34))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(2.5),math.rad(0),math.rad(0 + 1 * math.cos(sine / 34))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
RW.C0=clerp(RW.C0,cf(1,0.7,-1)*angles(math.rad(120),math.rad(0),math.rad(-50)),.3)
LW.C0=clerp(LW.C0,cf(-1,0.7,-1)*angles(math.rad(100),math.rad(0),math.rad(50)),.3)
end
for i = 0, 2, 0.1 do
swait()
coroutine.resume(coroutine.create(function()
huggedperson:FindFirstChildOfClass("Humanoid").Health = huggedperson:FindFirstChildOfClass("Humanoid").Health + 0.2
end))
rsiz = math.random(5,15)
sphereMK(math.random(1,4),0.15,"Add",root.CFrame*CFrame.new(math.random(-5,5),math.random(-8,-4),math.random(-5,5))*CFrame.Angles(math.rad(90),0,0),rsiz/20,rsiz/20,rsiz/20,0,MAINRUINCOLOR,0)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 2.5 * math.cos(sine / 28)),math.rad(10),math.rad(35)),.1)
huggedperson.Head.CFrame = root.CFrame*CFrame.new(0,1.25,-1.25)*CFrame.Angles(0,math.rad(180),0)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(7.5),math.rad(0),math.rad(-10 + 1 * math.cos(sine / 34))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(2.5),math.rad(0),math.rad(0 + 1 * math.cos(sine / 34))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
RW.C0=clerp(RW.C0,cf(1,0.7,-1)*angles(math.rad(120),math.rad(0),math.rad(-50)),.3)
LW.C0=clerp(LW.C0,cf(-1,0.7,-1)*angles(math.rad(100),math.rad(0),math.rad(50)),.3)
end
end
end
hum.WalkSpeed = storehumanoidWS
root.Anchored = false
if act == true then
coroutine.resume(coroutine.create(function()
huggedperson.Humanoid.WalkSpeed = 16
huggedperson:WaitForChild("HumanoidRootPart").Anchored = false
end))
end
hum.WalkSpeed = storehumanoidWS
attack = false
end

function TheoriesTran()
	attack = true
	hum.WalkSpeed = 0
newThemeCust("rbxassetid://1485663990",0,1.01,1.25)

local vel = Instance.new("BodyPosition", root)
vel.P = 10000
vel.D = 1000
vel.maxForce = Vector3.new(50000000000, 10e10, 50000000000)
vel.position = root.CFrame.p + vt(0,150,0)
wait(1)
sphere(1,"Divide",root.CFrame,vt(0,0,0),10,MAINRUINCOLOR)
for i = 0, 80, 0.1 do
		swait()
if math.random(1,3) == 1 then
slash(math.random(10,13)/10,2,false,"Round","Add","Out",root.CFrame*CFrame.new(0,3,0)*CFrame.Angles(math.rad(math.random(-360, 360)), math.rad(math.random(-360, 360)), math.rad(math.random(-360, 360))),vt(0.05,0.01,0.05),math.random(3,5),BrickColor.new("Black"))
end
sphereMKCharge(1,-1,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2.5,2.5,25,-0.025,BrickColor.new("Black"),0)
shakes(0.15,0.2)
RH.C0=clerp(RH.C0,cf(1,-0.4 - 0.05 * math.cos(sine / 32),-0.4)*angles(math.rad(5),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(1 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.4 - 0.05 * math.cos(sine / 32),-0.4)*angles(math.rad(5),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-1 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.02 + 0.02 * math.cos(sine / 32),1 + 0.05 * math.cos(sine / 32))*angles(math.rad(15 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0 - 1 * math.cos(sine / 44))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(22 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),-0.1)*angles(math.rad(160 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),-0.1)*angles(math.rad(160 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(33 - 3 * math.cos(sine / 45))),.1)
end
TheoriesMSGfunc("THEORIES",BrickColor.new("Cyan").Color,BrickColor.new("New Yeller").Color)
ModeOfGlitch = 102341
storehumanoidWS = 125
hum.WalkSpeed = 125
rainbowmode = false
chaosmode = false
RecolorTextAndRename("THEORIES",BrickColor.new("New Yeller").Color,BrickColor.new("Cyan").Color,"Code")
MAINRUINCOLOR = BrickColor.new("New Yeller")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Cyan"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Cyan"),0,MAINRUINCOLOR,true)
sphere(2.5,"Add",root.CFrame,vt(1,1,1),1,MAINRUINCOLOR)
for i = 0, 49 do
PixelBlock(1,math.random(1,20),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2,2,2,0.04,MAINRUINCOLOR,0)
end
for i = 0, 19 do
sphereMK(2.5,-1,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2.5,2.5,25,-0.025,MAINRUINCOLOR,0)
end
vel:Destroy()
local vel2 = Instance.new("BodyPosition", root)
vel2.P = 20000
vel2.D = 1000
vel2.maxForce = Vector3.new(50000000000, 10e10, 50000000000)
vel2.position = root.CFrame.p - vt(0,148,0)
wait(0.5)
sphere(2.5,"Add",root.CFrame,vt(1,1,1),3,MAINRUINCOLOR)
for i = 0, 49 do
PixelBlock(1,math.random(1,20),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2,2,2,0.04,MAINRUINCOLOR,0)
end
for i = 0, 19 do
sphereMK(2.5,-1,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),5.5,5.5,55,-0.055,MAINRUINCOLOR,0)
end
CFuncs["Sound"].Create("rbxassetid://239000203", root, 4, 1)
CFuncs["Sound"].Create("rbxassetid://1042716828", root, 2, 1)
CFuncs["Sound"].Create("rbxassetid://847061203", root, 3, 1)
coroutine.resume(coroutine.create(function()
wait(0.2)
vel2:Destroy()
end))
hum.WalkSpeed = storehumanoidWS
attack = false
end

function ViolentStrike()
attack = true
hum.WalkSpeed = 0
CFuncs["Sound"].Create("rbxassetid://136007472", rarm, 1, 1)
bosschatfunc("NO MORE!!",MAINRUINCOLOR.Color,0.7)
local orb = Instance.new("Part", char)
        orb.BrickColor = MAINRUINCOLOR
        orb.CanCollide = false
        orb.FormFactor = 3
        orb.Name = "Ring"
        orb.Material = "Neon"
        orb.Size = Vector3.new(1, 1, 1)
        orb.Transparency = 0
        orb.TopSurface = 0
        orb.BottomSurface = 0
        local orbm = Instance.new("SpecialMesh", orb)
        orbm.MeshType = "Sphere"
orbm.Name = "SizeMesh"
orbm.Scale = vt(2,2,2)
orb.CFrame = mouse.Hit
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = orb.CFrame.lookVector*100
bv.Parent = orb
local hitted = false
coroutine.resume(coroutine.create(function()
game:GetService("Debris"):AddItem(orb, 5)
orb.Transparency = 1
orb.Anchored = true
local elocacenter = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = orb.CFrame
elocacenter.Orientation = vt(0,0,0)
local eloca1 = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local eloca2 = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
eloca2.Anchored = true
eloca2.CFrame = elocacenter.CFrame
local eloca3 = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
eloca3.Anchored = true
eloca3.CFrame = elocacenter.CFrame
local eloca4 = CreateParta(m,1,1,"SmoothPlastic",BrickColor.random())
eloca4.Anchored = true
eloca4.CFrame = elocacenter.CFrame
local lookavec = 0 
local speeds = 0
CamShakeAll(27.5,30,char)
coroutine.resume(coroutine.create(function()
CFuncs["Sound"].Create("rbxassetid://419447292", elocacenter, 10,1)
sphere(5,"Add",elocacenter.CFrame,vt(0,0,0),1,MAINRUINCOLOR)
sphere(6,"Add",elocacenter.CFrame,vt(0,0,0),1,MAINRUINCOLOR)
sphere(7,"Add",elocacenter.CFrame,vt(0,0,0),1,MAINRUINCOLOR)
sphere(8,"Add",elocacenter.CFrame,vt(0,0,0),1,MAINRUINCOLOR)
sphere(9,"Add",elocacenter.CFrame,vt(0,0,0),1,MAINRUINCOLOR)
for i = 0, 24 do
swait()
lookavec = lookavec + 2
speeds = speeds + 1
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(speeds),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(lookavec,0,0)
PixelBlockNeg(2,math.random(1,2),"Add",eloca1.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)

eloca2.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
PixelBlockNeg(2,math.random(1,2),"Add",eloca2.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)

eloca3.CFrame = elocacenter.CFrame*CFrame.new(0,0,lookavec)
PixelBlockNeg(2,math.random(1,2),"Add",eloca3.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)

eloca4.CFrame = elocacenter.CFrame*CFrame.new(0,0,-lookavec)
PixelBlockNeg(2,math.random(1,2),"Add",eloca4.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)
end

local risen = 0
for i = 0, 176 do
swait()
lookavec = lookavec + 0.25
risen = risen + 0.05
speeds = speeds + 0.1
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(speeds),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(lookavec,0,0)
PixelBlockNeg(2,math.random(1+risen,2+risen),"Add",eloca1.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)

eloca2.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
PixelBlockNeg(2,math.random(1+risen,2+risen),"Add",eloca2.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)

eloca3.CFrame = elocacenter.CFrame*CFrame.new(0,0,lookavec)
PixelBlockNeg(2,math.random(1+risen,2+risen),"Add",eloca3.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)

eloca4.CFrame = elocacenter.CFrame*CFrame.new(0,0,-lookavec)
PixelBlockNeg(2,math.random(1+risen,2+risen),"Add",eloca4.CFrame*CFrame.Angles(math.rad(90 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),5,5,5,0.05,MAINRUINCOLOR,-2)
end

for i = 0, 176 do
swait()
lookavec = lookavec + 0.5
risen = risen + 0.05
speeds = speeds + 0.1
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(speeds),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(lookavec,0,0)

eloca2.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)

eloca3.CFrame = elocacenter.CFrame*CFrame.new(0,0,lookavec)

eloca4.CFrame = elocacenter.CFrame*CFrame.new(0,0,-lookavec)
end
end))
for i = 0, 12, 0.1 do
swait()
PixelBlockX(5,0.5,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
PixelBlockNeg(5,0.5,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
PixelBlockX(5,0.5,"Add",larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
PixelBlockNeg(5,0.5,"Add",larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.25 + 0.02 * math.cos(sine / 32),1 + 0.1 * math.cos(sine / 32))*angles(math.rad(-40 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-10 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(140 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(10 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(140 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-10 + 3 * math.cos(sine / 45))),.1)
end
coroutine.resume(coroutine.create(function()
for i, v in pairs(FindNearestHead(elocacenter.CFrame.p, 100)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
CamShakeAll(65,90,Character)
MagniDamage(elocacenter, 150, 20,50, 0, "Normal")
CFuncs["Sound"].Create("rbxassetid://468991944", char, 2, 1)
CFuncs["Sound"].Create("rbxassetid://533636230", char, 2.5, 0.75)
CFuncs["Sound"].Create("rbxassetid://419447292", char, 0.25,1)
CFuncs["Sound"].Create("rbxassetid://421328847", char, 0.25,1)
CFuncs["Sound"].Create("rbxassetid://919941001", char, 1.5,1.05)
sphere(1,"Add",elocacenter.CFrame,vt(100,90000,100),-0.25,MAINRUINCOLOR)
sphere(1,"Add",elocacenter.CFrame,vt(100,90000,100),0.5,MAINRUINCOLOR)
sphere(1,"Add",elocacenter.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(2,"Add",elocacenter.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(3,"Add",elocacenter.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(4,"Add",elocacenter.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(5,"Add",elocacenter.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(5,"Add",elocacenter.CFrame,vt(0,0,0),500,MAINRUINCOLOR)
for i = 0, 24 do
sphereMK(2,2,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,100,-0.25,MAINRUINCOLOR,0)
sphereMK(4,4,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,75,-0.25,MAINRUINCOLOR,0)
sphereMK(6,6,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,50,-0.25,MAINRUINCOLOR,0)
sphereMK(8,8,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,25,-0.25,MAINRUINCOLOR,0)
end
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",elocacenter)
eff.Texture = "rbxassetid://2092248396"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.new("Maroon").Color)
eff.Rate = 50000
eff.Lifetime = NumberRange.new(6,12)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,60,0),NumberSequenceKeypoint.new(0.2,0.75,0),NumberSequenceKeypoint.new(1,0.1,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.2,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(100,1000)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-100,100)
wait(0.6)
eff.Enabled = false
end))
wait(1.25)
for i, v in pairs(FindNearestHead(elocacenter.CFrame.p, 100*2)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
CamShakeAll(65,90,Character)
MagniDamage(elocacenter, 150*2, 20,50, 0, "Normal")
CFuncs["Sound"].Create("rbxassetid://468991944", char, 2, 1)
CFuncs["Sound"].Create("rbxassetid://533636230", char, 2.5, 0.75)
CFuncs["Sound"].Create("rbxassetid://419447292", char, 0.25,1)
CFuncs["Sound"].Create("rbxassetid://421328847", char, 0.25,1)
CFuncs["Sound"].Create("rbxassetid://919941001", char, 1.5,1.05)
sphere(1,"Add",elocacenter.CFrame,vt(150,90000,150),-0.5,MAINRUINCOLOR)
sphere(1,"Add",elocacenter.CFrame,vt(150,90000,150),1,MAINRUINCOLOR)
sphere(1,"Add",elocacenter.CFrame,vt(0,0,0),5*2,MAINRUINCOLOR)
sphere(2,"Add",elocacenter.CFrame,vt(0,0,0),5*2,MAINRUINCOLOR)
sphere(3,"Add",elocacenter.CFrame,vt(0,0,0),5*2,MAINRUINCOLOR)
sphere(4,"Add",elocacenter.CFrame,vt(0,0,0),5*2,MAINRUINCOLOR)
sphere(5,"Add",elocacenter.CFrame,vt(0,0,0),5*2,MAINRUINCOLOR)
sphere(5,"Add",elocacenter.CFrame,vt(0,0,0),500*2,MAINRUINCOLOR)
for i = 0, 24 do
sphereMK(2,2,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,100,-0.25*2,MAINRUINCOLOR,0)
sphereMK(4,4,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,75,-0.25*2,MAINRUINCOLOR,0)
sphereMK(6,6,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,50,-0.25*2,MAINRUINCOLOR,0)
sphereMK(8,8,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,25,-0.25*2,MAINRUINCOLOR,0)
end
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",elocacenter)
eff.Texture = "rbxassetid://2092248396"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.new("Maroon").Color)
eff.Rate = 50000
eff.Lifetime = NumberRange.new(6,12)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,90,0),NumberSequenceKeypoint.new(0.2,1.25,0),NumberSequenceKeypoint.new(1,0.1,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.2,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(125,1250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-100,100)
wait(1.25)
eff.Enabled = false
end))
wait(1.25)
for i, v in pairs(FindNearestHead(elocacenter.CFrame.p, 100*3)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
CamShakeAll(65,90,Character)
MagniDamage(elocacenter, 150*3, 20,50, 0, "Normal")
CFuncs["Sound"].Create("rbxassetid://468991944", char, 2, 1)
CFuncs["Sound"].Create("rbxassetid://533636230", char, 2.5, 0.75)
CFuncs["Sound"].Create("rbxassetid://419447292", char, 0.25,1)
CFuncs["Sound"].Create("rbxassetid://421328847", char, 0.25,1)
CFuncs["Sound"].Create("rbxassetid://919941001", char, 1.5,1.05)
sphere(1,"Add",elocacenter.CFrame,vt(225,90000,225),-0.25*3,MAINRUINCOLOR)
sphere(1,"Add",elocacenter.CFrame,vt(225,90000,225),0.5*3,MAINRUINCOLOR)
sphere(1,"Add",elocacenter.CFrame,vt(0,0,0),5*3,MAINRUINCOLOR)
sphere(2,"Add",elocacenter.CFrame,vt(0,0,0),5*3,MAINRUINCOLOR)
sphere(3,"Add",elocacenter.CFrame,vt(0,0,0),5*3,MAINRUINCOLOR)
sphere(4,"Add",elocacenter.CFrame,vt(0,0,0),5*3,MAINRUINCOLOR)
sphere(5,"Add",elocacenter.CFrame,vt(0,0,0),5*3,MAINRUINCOLOR)
sphere(5,"Add",elocacenter.CFrame,vt(0,0,0),500*3,MAINRUINCOLOR)
for i = 0, 24 do
sphereMK(2,2,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,100,-0.25*3,MAINRUINCOLOR,0)
sphereMK(4,4,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,75,-0.25*3,MAINRUINCOLOR,0)
sphereMK(6,6,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,50,-0.25*3,MAINRUINCOLOR,0)
sphereMK(8,8,"Add",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),25,25,25,-0.25*3,MAINRUINCOLOR,0)
end
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",elocacenter)
eff.Texture = "rbxassetid://2092248396"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.new("Maroon").Color)
eff.Rate = 50000
eff.Lifetime = NumberRange.new(6,12)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,120,0),NumberSequenceKeypoint.new(0.2,2.5,0),NumberSequenceKeypoint.new(1,0.1,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.2,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(150,1500)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-100,100)
wait(1.25)
eff.Enabled = false
end))
wait(0.5)
chatfunc("That was fun..",MAINRUINCOLOR.Color,"Inverted","Arcade",1)
CFuncs["EchoSound"].Create("rbxassetid://1535995263", char, 4.75, 1,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://1535995263", root, 9, 1,0,10,0.15,0.5,1)
end))
for i = 0, 4, 0.1 do
swait()
PixelBlockX(5,0.5,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
PixelBlockNeg(5,0.5,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
PixelBlockX(5,0.5,"Add",larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
PixelBlockNeg(5,0.5,"Add",larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),1,1,1,0.01,MAINRUINCOLOR,0)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.25 + 0.02 * math.cos(sine / 32),1 + 0.1 * math.cos(sine / 32))*angles(math.rad(30 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(25 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(80 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(10 + 3 * math.cos(sine / 45))),.2)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(80 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-10 + 3 * math.cos(sine / 45))),.2)
end
hum.WalkSpeed = storehumanoidWS
attack = false
coroutine.resume(coroutine.create(function()
wait(10)
elocacenter:Destroy()
eloca1:Destroy()
eloca2:Destroy()
eloca3:Destroy()
eloca4:Destroy()
end))
end))
end

function ExtCalbeam()
	local lookavec = 0 
	local mult = 1
	local keptcolor = MAINRUINCOLOR
local dis = CreateParta(char,0,1,"Neon",keptcolor)
for i = 0, 2 do
CFuncs["Sound"].Create("rbxassetid://335657174", dis, 10, 0.5)
end
dis.CFrame = root.CFrame*CFrame.new(0,2,-3)
CreateMesh(dis,"Sphere",4,4,4)
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-2,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(2,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.FaceCamera = true
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 3
local efec = Instance.new("ParticleEmitter",dis)
efec.Texture = "rbxassetid://144580273"
efec.LightEmission = 1
efec.Color = ColorSequence.new(keptcolor.Color)
efec.Rate = 500000
efec.Lifetime = NumberRange.new(1)
efec.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,2,0),NumberSequenceKeypoint.new(1,0,0)})
efec.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,1,0)})
efec.Drag = 5
efec.Rotation = NumberRange.new(-500,500)
efec.VelocitySpread = 9000
efec.RotSpeed = NumberRange.new(-500,500)
local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*250
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 15)
local hitted = false
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
	shakes(1,1)
	efec.Enabled = false
	dis.Anchored = true
	dis.Transparency = 1
	local elocacenter = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = dis.CFrame*CFrame.new(0,1,0)
elocacenter.Orientation = Vector3.new(0,0,0)
local eloca1 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca1)
at1.Position = vt(0,20,0)
local at2 = Instance.new("Attachment",eloca1)
at2.Position = vt(0,-20,0)
local at1b = Instance.new("Attachment",eloca1)
at1b.Position = vt(0,0,100)
local at2b = Instance.new("Attachment",eloca1)
at2b.Position = vt(0,0,-100)
local trl = Instance.new('Trail',eloca1)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(keptcolor.Color)
trl.Lifetime = 4
local trl2 = trl:Clone()
trl2.Parent = eloca1
trl2.Attachment0 = at1b
trl2.Attachment1 = at2b
trl2.Texture = "rbxassetid://2108945559"
trl2.Lifetime = 2
local eff = Instance.new("ParticleEmitter",eloca1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,3)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,50,0),NumberSequenceKeypoint.new(0.2,5,0),NumberSequenceKeypoint.new(0.8,5,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca2 = eloca1:Clone()
eloca2.Parent = elocacenter
local eloca3 = eloca1:Clone()
eloca3.Parent = elocacenter
local eloca4 = eloca1:Clone()
eloca4.Parent = elocacenter
sphere2(2,"Add",elocacenter.CFrame,vt(1,1,1),2,2,2,keptcolor)
sphere2(3,"Add",elocacenter.CFrame,vt(1,1,1),4,4,4,keptcolor)
sphere2(4,"Add",elocacenter.CFrame,vt(1,1,1),5,5,5,keptcolor)
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),6,6,6,keptcolor)
for i = 0, 2 do
CFuncs["Sound"].Create("rbxassetid://419447292", elocacenter, 8, 1)
CFuncs["Sound"].Create("rbxassetid://1192402877", elocacenter, 10, 0.5)
end
local effx = Instance.new("ParticleEmitter",elocacenter)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(keptcolor.Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
coroutine.resume(coroutine.create(function()
	wait(0.05)
	effx.Enabled = false
end))
coroutine.resume(coroutine.create(function()
for i = 0, 9, 0.1 do
	swait()
	mult = mult + 0.5
lookavec = lookavec + 0.06*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i = 0, 11, 0.1 do
	swait()
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
shakes(1.5,1.5)
MagniDamage(elocacenter, 200, 50,99, 0, "Normal")
coroutine.resume(coroutine.create(function()
for i, v in pairs(FindNearestHead(elocacenter.CFrame.p, 150)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
end))
local effe = Instance.new("ParticleEmitter",elocacenter)
effe.Texture = "rbxassetid://2273224484"
effe.LightEmission = 1
effe.Color = ColorSequence.new(keptcolor.Color)
effe.Rate = 500000
effe.Lifetime = NumberRange.new(3,5)
effe.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,150,0),NumberSequenceKeypoint.new(0.2,15,0),NumberSequenceKeypoint.new(0.8,15,0),NumberSequenceKeypoint.new(1,0,0)})
effe.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effe.Speed = NumberRange.new(250,1200)
effe.Drag = 5
effe.Rotation = NumberRange.new(-500,500)
effe.VelocitySpread = 9000
effe.RotSpeed = NumberRange.new(-50,50)
coroutine.resume(coroutine.create(function()
	effx.Enabled = true
	wait(0.15)
	effx.Enabled = false
	wait(0.25)
	effe.Enabled = false
end))
coroutine.resume(coroutine.create(function()
	local elocor = elocacenter
local lookavec = 0 
	local mult = 1
local elocacenter = CreateParta(elocor,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = elocor.CFrame
local eloca1 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca1)
at1.Position = vt(0,10,0)
local at2 = Instance.new("Attachment",eloca1)
at2.Position = vt(0,-10,0)
local trl = Instance.new('Trail',eloca1)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(keptcolor.Color)
trl.Lifetime = 8
local eff = Instance.new("ParticleEmitter",eloca1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,1)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca2 = eloca1:Clone()
eloca2.Parent = elocacenter
local eloca3 = eloca1:Clone()
eloca3.Parent = elocacenter
local eloca4 = eloca1:Clone()
eloca4.Parent = elocacenter
coroutine.resume(coroutine.create(function()
for i = 0, 19, 0.1 do
	swait()
	mult = mult + 0.25
lookavec = lookavec + 0.05*mult
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/10),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(40+lookavec/5,-15+lookavec*2,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.new(-40-lookavec/5,-15+lookavec*2,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.new(0,-15+lookavec*2,40+lookavec/5)
eloca4.CFrame = elocacenter.CFrame*CFrame.new(0,-15+lookavec*2,-40-lookavec/5)
end
for i,v in pairs(elocacenter:GetDescendants()) do
	if v:IsA("ParticleEmitter") then
		v.Enabled = false
	end
end
wait(6)
elocacenter:Destroy()
end))
end))
CFuncs["Sound"].Create("rbxassetid://763717897", char, 3, 1)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 3, 0.75)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 4, 0.5)
CFuncs["Sound"].Create("rbxassetid://1192402877", char, 6,0.5)
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 2.5,1)
CFuncs["Sound"].Create("rbxassetid://763718160", char, 3, 0.75)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,20,0,0,0,root,true,-5,3)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,40,0,0,0,root,true,-5,3)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,60,0,0,0,root,true,-5,3)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,80,0,0,0,root,true,-5,3)
sphere2(2,"Add",elocacenter.CFrame,vt(10,10000,10),2,2,2,keptcolor)
sphere2(1,"Add",elocacenter.CFrame,vt(10,10000,10),2,2,2,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(10,10,10),5,5,5,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(10,10,10),7.5,7.5,7.5,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(10,10,10),10,10,10,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(10,10,10),2.5,2.5,2.5,keptcolor)
for i = 0, 29 do
		slash(math.random(10,30)/10,5,true,"Round","Add","Out",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(500,1000)/250,BrickColor.new("White"))
	end
for i = 0, 9, 0.1 do
	swait()
	mult = mult - 0.5
lookavec = lookavec - 0.06*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i,v in pairs(elocacenter:GetDescendants()) do
	if v:IsA("ParticleEmitter") then
		v.Enabled = false
	end
end
wait(6)
elocacenter:Destroy()
end))
end
end)
end))	
end

function ExtCatbeam()
	local lookavec = 0 
	local mult = 1
	local keptcolor = MAINRUINCOLOR
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("SO DO YOU WANT IT TO BE STOP?",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("YALL ARE USELESS!!",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("HAHAHAHA",MAINRUINCOLOR.Color,1)
end
local dis = CreateParta(char,0,1,"Neon",keptcolor)
for i = 0, 2 do
CFuncs["Sound"].Create("rbxassetid://335657174", dis, 10, 0.5)
end
dis.CFrame = root.CFrame*CFrame.new(0,2,-3)
CreateMesh(dis,"Sphere",4,4,4)
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-2,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(2,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.FaceCamera = true
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 3
local efec = Instance.new("ParticleEmitter",dis)
efec.Texture = "rbxassetid://144580273"
efec.LightEmission = 1
efec.Color = ColorSequence.new(keptcolor.Color)
efec.Rate = 500000
efec.Lifetime = NumberRange.new(1)
efec.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,2,0),NumberSequenceKeypoint.new(1,0,0)})
efec.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,1,0)})
efec.Drag = 5
efec.Rotation = NumberRange.new(-500,500)
efec.VelocitySpread = 9000
efec.RotSpeed = NumberRange.new(-500,500)
local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*250
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 15)
local hitted = false
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
	shakes(1,1)
	efec.Enabled = false
	dis.Anchored = true
	dis.Transparency = 1
	local elocacenter = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = dis.CFrame*CFrame.new(0,1,0)
elocacenter.Orientation = Vector3.new(0,0,0)
local eloca1 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca1)
at1.Position = vt(0,20,0)
local at2 = Instance.new("Attachment",eloca1)
at2.Position = vt(0,-20,0)
local at1b = Instance.new("Attachment",eloca1)
at1b.Position = vt(0,0,100)
local at2b = Instance.new("Attachment",eloca1)
at2b.Position = vt(0,0,-100)
local trl = Instance.new('Trail',eloca1)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(keptcolor.Color)
trl.Lifetime = 4
local trl2 = trl:Clone()
trl2.Parent = eloca1
trl2.Attachment0 = at1b
trl2.Attachment1 = at2b
trl2.Texture = "rbxassetid://2108945559"
trl2.Lifetime = 2
local eff = Instance.new("ParticleEmitter",eloca1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,3)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,50,0),NumberSequenceKeypoint.new(0.2,5,0),NumberSequenceKeypoint.new(0.8,5,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca2 = eloca1:Clone()
eloca2.Parent = elocacenter
local eloca3 = eloca1:Clone()
eloca3.Parent = elocacenter
local eloca4 = eloca1:Clone()
eloca4.Parent = elocacenter
sphere2(2,"Add",elocacenter.CFrame,vt(1,1,1),2,2,2,keptcolor)
sphere2(3,"Add",elocacenter.CFrame,vt(1,1,1),4,4,4,keptcolor)
sphere2(4,"Add",elocacenter.CFrame,vt(1,1,1),5,5,5,keptcolor)
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),6,6,6,keptcolor)
for i = 0, 2 do
CFuncs["Sound"].Create("rbxassetid://419447292", elocacenter, 8, 1)
CFuncs["Sound"].Create("rbxassetid://1192402877", elocacenter, 10, 0.5)
end
local effx = Instance.new("ParticleEmitter",elocacenter)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(keptcolor.Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
coroutine.resume(coroutine.create(function()
	wait(0.05)
	effx.Enabled = false
end))
coroutine.resume(coroutine.create(function()
for i = 0, 9*2, 0.1 do
	swait()
	mult = mult + 0.5
lookavec = lookavec + 0.06*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i = 0, 24, 0.1 do
	swait()
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
shakes(3,1.5)
MagniDamage(elocacenter, 500, 50,99, 0, "Normal")
coroutine.resume(coroutine.create(function()
for i, v in pairs(FindNearestHead(elocacenter.CFrame.p, 420)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
end))
local effe = Instance.new("ParticleEmitter",elocacenter)
effe.Texture = "rbxassetid://2273224484"
effe.LightEmission = 1
effe.Color = ColorSequence.new(keptcolor.Color)
effe.Rate = 500000
effe.Lifetime = NumberRange.new(3,5)
effe.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,150,0),NumberSequenceKeypoint.new(0.2,15,0),NumberSequenceKeypoint.new(0.8,15,0),NumberSequenceKeypoint.new(1,0,0)})
effe.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effe.Speed = NumberRange.new(250,1200)
effe.Drag = 5
effe.Rotation = NumberRange.new(-500,500)
effe.VelocitySpread = 9000
effe.RotSpeed = NumberRange.new(-50,50)
coroutine.resume(coroutine.create(function()
	effx.Enabled = true
	wait(0.15)
	effx.Enabled = false
	wait(0.25)
	effe.Enabled = false
end))
coroutine.resume(coroutine.create(function()
	local elocor = elocacenter
local lookavec = 0 
	local mult = 1
local elocacenter = CreateParta(elocor,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = elocor.CFrame
local eloca1 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca1)
at1.Position = vt(0,10,0)
local at2 = Instance.new("Attachment",eloca1)
at2.Position = vt(0,-10,0)
local trl = Instance.new('Trail',eloca1)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(keptcolor.Color)
trl.Lifetime = 8
local eff = Instance.new("ParticleEmitter",eloca1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,1)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(0.8,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca2 = eloca1:Clone()
eloca2.Parent = elocacenter
local eloca3 = eloca1:Clone()
eloca3.Parent = elocacenter
local eloca4 = eloca1:Clone()
eloca4.Parent = elocacenter
coroutine.resume(coroutine.create(function()
for i = 0, 19, 0.1 do
	swait()
	mult = mult + 0.25
lookavec = lookavec + 0.05*mult
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/10),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(40+lookavec/5,-15+lookavec*2,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.new(-40-lookavec/5,-15+lookavec*2,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.new(0,-15+lookavec*2,40+lookavec/5)
eloca4.CFrame = elocacenter.CFrame*CFrame.new(0,-15+lookavec*2,-40-lookavec/5)
end
for i,v in pairs(elocacenter:GetDescendants()) do
	if v:IsA("ParticleEmitter") then
		v.Enabled = false
	end
end
wait(6)
elocacenter:Destroy()
end))
end))
CFuncs["Sound"].Create("rbxassetid://763717897", char, 4, 1)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 4, 0.75)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 5, 0.5)
CFuncs["Sound"].Create("rbxassetid://1192402877", char, 7,0.5)
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 3.5,1)
CFuncs["Sound"].Create("rbxassetid://763718160", char, 4, 0.75)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,40*2,0,0,0,root,true,-5,3)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,80*2,0,0,0,root,true,-5,3)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,120*2,0,0,0,root,true,-5,3)
symbolizeBlink(elocacenter,0,144580273,keptcolor.Color,160*2,0,0,0,root,true,-5,3)
sphere2(2,"Add",elocacenter.CFrame,vt(110,10000,110),4,4,4,keptcolor)
sphere2(1,"Add",elocacenter.CFrame,vt(110,10000,110),4,4,4,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(110,110,110),10,10,10,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(110,110,110),15,15,15,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(110,110,110),20,20,20,keptcolor)
sphere2(2,"Add",elocacenter.CFrame,vt(110,110,110),5,5,5,keptcolor)
for i = 0, 29 do
		slash(math.random(10,30)/10,5,true,"Round","Add","Out",elocacenter.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(500,1000)/150,BrickColor.new("White"))
	end
for i = 0, 9, 0.1 do
	swait()
	mult = mult - 0.5
lookavec = lookavec - 0.06*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i,v in pairs(elocacenter:GetDescendants()) do
	if v:IsA("ParticleEmitter") then
		v.Enabled = false
	end
end
wait(6)
elocacenter:Destroy()
end))
end
end)
end))	
end

function CalMets()
attack = true
hum.WalkSpeed = 0
	local lookavec = 0 
	local mult = 1
	local keptcolor = MAINRUINCOLOR
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("Beyonder..",MAINRUINCOLOR.Color,2.5)
elseif radm == 2 then
bosschatfunc("Sky's upon yall.",MAINRUINCOLOR.Color,2.5)
elseif radm == 3 then
bosschatfunc("Look up.",MAINRUINCOLOR.Color,2.5)
end
CFuncs["Sound"].Create("rbxassetid://136007472", root, 7, 1.25)
for i = 0,4,0.1 do
		swait()
slash(math.random(25,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,10,1)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.2,0.01,0.2),-0.2,keptcolor)
sphere2(3,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(3,3,3),0.06,0.06,0.06,MAINRUINCOLOR)
            RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 5 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(-35),math.rad(0),math.rad(0)),.1)
RW.C0 = clerp(RW.C0, CFrame.new(1.25, 1, -0.5) * angles(math.rad(170), math.rad(0), math.rad(-20)), 0.1)
LW.C0 = clerp(LW.C0, CFrame.new(-1.25, 1, -0.5) * angles(math.rad(170), math.rad(0), math.rad(20)), 0.1)
end
sphere2(2,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(3,3,3),0.6,0.6,0.6,keptcolor)
sphere2(4,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(3,3,3),0.6,0.6,0.6,keptcolor)
sphere2(3,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(1,10000,1),0.06,0.06,0.06,keptcolor)
local elocacenter = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = root.CFrame*CFrame.new(0,150,0)
local eloca1 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca1)
at1.Position = vt(0,30,0)
local at2 = Instance.new("Attachment",eloca1)
at2.Position = vt(0,-30,0)
local at1b = Instance.new("Attachment",eloca1)
at1b.Position = vt(0,0,180)
local at2b = Instance.new("Attachment",eloca1)
at2b.Position = vt(0,0,-180)
local trl = Instance.new('Trail',eloca1)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(keptcolor.Color)
trl.Lifetime = 4
local trl2 = trl:Clone()
trl2.Parent = eloca1
trl2.Attachment0 = at1b
trl2.Attachment1 = at2b
trl2.Texture = "rbxassetid://2108945559"
trl2.Lifetime = 2
local eff = Instance.new("ParticleEmitter",eloca1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,3)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,50,0),NumberSequenceKeypoint.new(0.2,5,0),NumberSequenceKeypoint.new(0.8,5,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca2 = eloca1:Clone()
eloca2.Parent = elocacenter
local eloca3 = eloca1:Clone()
eloca3.Parent = elocacenter
local eloca4 = eloca1:Clone()
eloca4.Parent = elocacenter
shakes(0.5,0.5)
sphere2(2,"Add",elocacenter.CFrame,vt(1,1,1),2,2,2,keptcolor)
sphere2(3,"Add",elocacenter.CFrame,vt(1,1,1),4,4,4,keptcolor)
sphere2(4,"Add",elocacenter.CFrame,vt(1,1,1),5,5,5,keptcolor)
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),6,6,6,keptcolor)
sphere2(3,"Add",elocacenter.CFrame,vt(1,1,1),25,0.1,25,keptcolor)
sphere2(4,"Add",elocacenter.CFrame,vt(1,1,1),25,0.1,25,keptcolor)
CFuncs["Sound"].Create("rbxassetid://419447292", char, 4, 1)
local effx = Instance.new("ParticleEmitter",elocacenter)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(keptcolor.Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
coroutine.resume(coroutine.create(function()
	wait(0.05)
	effx.Enabled = false
end))
coroutine.resume(coroutine.create(function()
for i = 0, 9, 0.1 do
	swait()
	mult = mult + 0.5
lookavec = lookavec + 0.1*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i = 0, 19 do
for i = 0, 1, 0.1 do
	swait()
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
local lb = Instance.new("Part")
lb.Color = keptcolor.Color
lb.CanCollide = false
lb.Material = "Neon"
lb.Anchored = true
lb.TopSurface = 0
lb.BottomSurface = 0
lb.Transparency = 0
lb.Size = vt(1,1,1)
lb.CFrame = elocacenter.CFrame*CFrame.new(math.random(-150,150),0,math.random(-150,150))*CFrame.Angles(math.rad(-90 + math.random(-15,15)),0,math.rad(math.random(-15,15)))
lb.Anchored = false
 lb.Parent = char
local thingery = Instance.new("SpecialMesh",lb)
     thingery.MeshType = "Sphere"
thingery.Scale = vt(20,20,20)
game:GetService("Debris"):AddItem(lb, 10)
  local bv = Instance.new("BodyVelocity")
  bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
  bv.velocity = lb.CFrame.lookVector*math.random(125,350)
  bv.Parent = lb
sphere(2.5,"Add",lb.CFrame,vt(50,50,0),0.25,keptcolor)
sphere(5,"Add",lb.CFrame,vt(50,50,0),0.5,keptcolor)
CFuncs["Sound"].Create("rbxassetid://633627961",lb, 10, 1)
CFuncs["Sound"].Create("rbxassetid://1002081188", lb, 10, 1)
CFuncs["Sound"].Create("rbxassetid://741272936", lb, 10, 1)
CFuncs["Sound"].Create("rbxassetid://1192402877", lb, 10, 1)
local hitted = false
local tril = Instance.new("ParticleEmitter",lb)
tril.Texture = "rbxassetid://144580273" -- 144580273 74564879
tril.LightEmission = 1
tril.Color = ColorSequence.new(keptcolor.Color)
tril.Rate = 500000
tril.Lifetime = NumberRange.new(0.5,1)
tril.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(1,0,0)})
tril.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
tril.Speed = NumberRange.new(0,10)
tril.Drag = 5
tril.Rotation = NumberRange.new(-500,500)
tril.VelocitySpread = 9000
tril.RotSpeed = NumberRange.new(-50,50)
game:GetService("Debris"):AddItem(a, 0.1)

coroutine.resume(coroutine.create(function()
lb.Touched:connect(function(hit)
if hitted == false and hit.Parent ~= char then
hitted = true
lb.Transparency = 1
lb.Anchored = true
tril.Enabled = false
CFuncs["EchoSound"].Create("rbxassetid://675172759", lb, 8, 0.8,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://782200047", lb, 10, 1.1,0,10,0.15,0.5,1)
MagniDamage(lb, 60, 50,100, 0, "Normal")
CamShakeAll(25,15,char)
local effx = Instance.new("ParticleEmitter",lb)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(keptcolor.Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
sphere(5,"Add",lb.CFrame,vt(20,20,20),1,keptcolor)
sphere(6,"Add",lb.CFrame,vt(20,20,20),2,keptcolor)
wait(0.05)
effx.Enabled = false
end
end)
end))
end
for i = 0, 9, 0.1 do
	swait()
	mult = mult - 0.5
lookavec = lookavec - 0.1*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i,v in pairs(elocacenter:GetDescendants()) do
	if v:IsA("ParticleEmitter") then
		v.Enabled = false
	end
end
wait(6)
elocacenter:Destroy()
end))
attack = false
hum.WalkSpeed = storehumanoidWS	
end

function CatMets()
attack = true
hum.WalkSpeed = 0
	local lookavec = 0 
	local mult = 1
	local keptcolor = MAINRUINCOLOR
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("YOUR FAITH IS UPON THIS SKY!!",MAINRUINCOLOR.Color,2.5)
elseif radm == 2 then
bosschatfunc("DISASTER SKY!!",MAINRUINCOLOR.Color,2.5)
elseif radm == 3 then
bosschatfunc("YOU SHOULD THINK ABOUT THAT AGAIN!!",MAINRUINCOLOR.Color,2.5)
end
CFuncs["Sound"].Create("rbxassetid://136007472", root, 7, 1.25)
for i = 0,4,0.1 do
		swait()
slash(math.random(25,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,10,1)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.5,0.01,0.5),-0.5,keptcolor)
sphere2(3,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(3,3,3),0.09,0.09,0.09,MAINRUINCOLOR)
            RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 5 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 43),0 - 0.25 * math.cos(sine / 53),6 + 1 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(-35),math.rad(0),math.rad(0)),.1)
RW.C0 = clerp(RW.C0, CFrame.new(1.25, 1, -0.5) * angles(math.rad(170), math.rad(0), math.rad(-20)), 0.1)
LW.C0 = clerp(LW.C0, CFrame.new(-1.25, 1, -0.5) * angles(math.rad(170), math.rad(0), math.rad(20)), 0.1)
end
sphere2(2,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(3,3,3),0.9,0.9,0.9,keptcolor)
sphere2(4,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(3,3,3),0.9,0.9,0.9,keptcolor)
sphere2(3,"Add",root.CFrame*CFrame.new(0,10,0) + root.CFrame.lookVector*1,vt(1,10000,1),0.09,0.09,0.09,keptcolor)
local elocacenter = CreateParta(char,1,1,"SmoothPlastic",BrickColor.random())
elocacenter.Anchored = true
elocacenter.CFrame = root.CFrame*CFrame.new(0,235,0)
local eloca1 = CreateParta(elocacenter,1,1,"SmoothPlastic",BrickColor.random())
eloca1.Anchored = true
eloca1.CFrame = elocacenter.CFrame
local at1 = Instance.new("Attachment",eloca1)
at1.Position = vt(0,30,0)
local at2 = Instance.new("Attachment",eloca1)
at2.Position = vt(0,-30,0)
local at1b = Instance.new("Attachment",eloca1)
at1b.Position = vt(0,0,180)
local at2b = Instance.new("Attachment",eloca1)
at2b.Position = vt(0,0,-180)
local trl = Instance.new('Trail',eloca1)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(keptcolor.Color)
trl.Lifetime = 4
local trl2 = trl:Clone()
trl2.Parent = eloca1
trl2.Attachment0 = at1b
trl2.Attachment1 = at2b
trl2.Texture = "rbxassetid://2108945559"
trl2.Lifetime = 2
local eff = Instance.new("ParticleEmitter",eloca1)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(keptcolor.Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,3)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,50,0),NumberSequenceKeypoint.new(0.2,5,0),NumberSequenceKeypoint.new(0.8,5,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(20,250)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eloca2 = eloca1:Clone()
eloca2.Parent = elocacenter
local eloca3 = eloca1:Clone()
eloca3.Parent = elocacenter
local eloca4 = eloca1:Clone()
eloca4.Parent = elocacenter
shakes(0.5,0.5)
sphere2(2,"Add",elocacenter.CFrame,vt(1,1,1),2,2,2,keptcolor)
sphere2(3,"Add",elocacenter.CFrame,vt(1,1,1),4,4,4,keptcolor)
sphere2(4,"Add",elocacenter.CFrame,vt(1,1,1),5,5,5,keptcolor)
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),6,6,6,keptcolor)
sphere2(3,"Add",elocacenter.CFrame,vt(1,1,1),25,0.1,25,keptcolor)
sphere2(4,"Add",elocacenter.CFrame,vt(1,1,1),25,0.1,25,keptcolor)
local effx = Instance.new("ParticleEmitter",elocacenter)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(keptcolor.Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
coroutine.resume(coroutine.create(function()
	wait(0.05)
	effx.Enabled = false
end))
coroutine.resume(coroutine.create(function()
for i = 0, 9*2, 0.1 do
	swait()
	mult = mult + 0.5
lookavec = lookavec + 0.1*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i = 0, 272.5 do
for i = 0, 0.175, 0.1 do
	swait()
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
local lb = Instance.new("Part")
lb.Color = keptcolor.Color
lb.CanCollide = false
lb.Material = "Neon"
lb.Anchored = true
lb.TopSurface = 0
lb.BottomSurface = 0
lb.Transparency = 0
lb.Size = vt(1,1,1)
lb.CFrame = elocacenter.CFrame*CFrame.new(math.random(-525,525),0,math.random(-525,525))*CFrame.Angles(math.rad(-90 + math.random(-30,30)),0,math.rad(math.random(-30,30)))
lb.Anchored = false
 lb.Parent = char
local thingery = Instance.new("SpecialMesh",lb)
     thingery.MeshType = "Sphere"
thingery.Scale = vt(20,20,20)
game:GetService("Debris"):AddItem(lb, 10)
  local bv = Instance.new("BodyVelocity")
  bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
  bv.velocity = lb.CFrame.lookVector*math.random(200,525)
  bv.Parent = lb
sphere(2.5,"Add",lb.CFrame,vt(50,50,0),0.25,keptcolor)
sphere(5,"Add",lb.CFrame,vt(50,50,0),0.5,keptcolor)
CFuncs["Sound"].Create("rbxassetid://633627961",lb, 10, 1)
CFuncs["Sound"].Create("rbxassetid://1002081188", lb, 10, 1)
CFuncs["Sound"].Create("rbxassetid://741272936", lb, 10, 1)
CFuncs["Sound"].Create("rbxassetid://1192402877", lb, 10, 1)
local hitted = false
local tril = Instance.new("ParticleEmitter",lb)
tril.Texture = "rbxassetid://144580273" -- 144580273 74564879
tril.LightEmission = 1
tril.Color = ColorSequence.new(keptcolor.Color)
tril.Rate = 500000
tril.Lifetime = NumberRange.new(0.5,1)
tril.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(1,0,0)})
tril.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
tril.Speed = NumberRange.new(0,10)
tril.Drag = 5
tril.Rotation = NumberRange.new(-500,500)
tril.VelocitySpread = 9000
tril.RotSpeed = NumberRange.new(-50,50)
game:GetService("Debris"):AddItem(a, 0.1)

coroutine.resume(coroutine.create(function()
lb.Touched:connect(function(hit)
if hitted == false and hit.Parent ~= char then
hitted = true
lb.Transparency = 1
lb.Anchored = true
tril.Enabled = false
CFuncs["EchoSound"].Create("rbxassetid://675172759", lb, 8, 0.8,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://782200047", lb, 10, 1.1,0,10,0.15,0.5,1)
MagniDamage(lb, 60, 20,90, 0, "Normal")
CamShakeAll(25,15,char)
local effx = Instance.new("ParticleEmitter",lb)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(keptcolor.Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
sphere(5,"Add",lb.CFrame,vt(20,20,20),1,keptcolor)
sphere(6,"Add",lb.CFrame,vt(20,20,20),2,keptcolor)
wait(0.05)
effx.Enabled = false
end
end)
end))
end
for i = 0, 9, 0.1 do
	swait()
	mult = mult - 0.5
lookavec = lookavec - 0.1*mult
sphere2(5,"Add",elocacenter.CFrame,vt(1,1,1),0.05*lookavec/2,0.001,0.05*lookavec/2,keptcolor)
elocacenter.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(5*mult/20),0)
eloca1.CFrame = elocacenter.CFrame*CFrame.new(-lookavec,0,0)
eloca2.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(90),0)*CFrame.new(-lookavec,0,0)
eloca3.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(180),0)*CFrame.new(-lookavec,0,0)
eloca4.CFrame = elocacenter.CFrame*CFrame.Angles(0,math.rad(270),0)*CFrame.new(-lookavec,0,0)
end
for i,v in pairs(elocacenter:GetDescendants()) do
	if v:IsA("ParticleEmitter") then
		v.Enabled = false
	end
end
wait(6)
elocacenter:Destroy()
end))
attack = false
hum.WalkSpeed = storehumanoidWS	
end

function EternalChaosOrb()
attack = true
hum.WalkSpeed = 1
CFuncs["EchoSound"].Create("rbxassetid://1448033299", char, 3, 1,0,10,0.15,0.5,1)
CFuncs["EchoSound"].Create("rbxassetid://1448033299", root, 10, 1,0,10,0.15,0.5,1)
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("How do you like this..",MAINRUINCOLOR.Color,1)
elseif radm == 2 then
bosschatfunc("The Eternal life..",MAINRUINCOLOR.Color,1)
elseif radm == 3 then
bosschatfunc("Orby..",MAINRUINCOLOR.Color,1)
end
local keptcolor = MAINRUINCOLOR
CFuncs["Sound"].Create("rbxassetid://1042700914", root, 5, 0.25)
for i = 0,14,0.1 do
		swait()
sphere2(8,"Add",rarm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,MAINRUINCOLOR,MAINRUINCOLOR.Color)
sphere2(8,"Add",larm.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Lime green"),Color3.new(0,1,0))
slash(math.random(25,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,6,1)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.2,0.01,0.2),-0.2,BrickColor.new(0,MRANDOM(0,1),0))
sphere2(3,"Add",root.CFrame*CFrame.new(0,6,0) + root.CFrame.lookVector*1,vt(3,3,3),0.06,0.06,0.06,MAINRUINCOLOR)
            RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0,0,0)* angles(math.rad(0),math.rad(0),math.rad(0)),0.1)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(-35),math.rad(0),math.rad(0)),.1)
RW.C0 = clerp(RW.C0, CFrame.new(1.25, 1, -0.5) * angles(math.rad(170), math.rad(0), math.rad(-20)), 0.1)
LW.C0 = clerp(LW.C0, CFrame.new(-1.25, 1, -0.5) * angles(math.rad(170), math.rad(0), math.rad(20)), 0.1)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 25),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1.5),math.rad(0),math.rad(0)),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 25),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(0)),.1)
end
local dis = CreateParta(char,0,1,"Neon",BrickColor.new("Lime green"))
for i = 0, 4 do
CFuncs["Sound"].Create("rbxassetid://335657174", dis, 10, 0.5)
end
dis.CFrame = root.CFrame*CFrame.new(0,5,-3)
CreateMesh(dis,"Sphere",10,10,10)
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-5,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(5,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.FaceCamera = true
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(dis.Color)
trl.Lifetime = 3
local efec = Instance.new("ParticleEmitter",dis)
efec.Texture = "rbxassetid://2109052855"
efec.LightEmission = 1
efec.Color = ColorSequence.new(Color3.new(0.5,0,1))
efec.Rate = 5
efec.Lifetime = NumberRange.new(3)
efec.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,25,0),NumberSequenceKeypoint.new(0.2,50,0),NumberSequenceKeypoint.new(0.6,35,0),NumberSequenceKeypoint.new(0.8,50,0),NumberSequenceKeypoint.new(1,75,0)})
efec.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.25,0),NumberSequenceKeypoint.new(0.6,0.25,0),NumberSequenceKeypoint.new(1,1,0)})
efec.Drag = 5
efec.LockedToPart = true
efec.Rotation = NumberRange.new(-500,500)
efec.VelocitySpread = 9000
efec.RotSpeed = NumberRange.new(-500,500)
local a = Instance.new("Part",workspace)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = bc("Bright red")
a.Material = "Neon"
a.Transparency = 1
	a.CanCollide = false
	local ray = Ray.new(
	    dis.CFrame.p,                           -- origin
	    (mouse.Hit.p - dis.CFrame.p).unit * 500 -- direction
	) 
	local ignore = dis
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (dis.CFrame.p - position).magnitude
	a.Size = Vector3.new(0.1, 0.1, 0.1)
	a.CFrame = CFrame.new(dis.CFrame.p, position) * CFrame.new(0, 0, 0)
dis.CFrame = a.CFrame
a:Destroy()
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*100
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 15)
local hitted = false
coroutine.resume(coroutine.create(function()
	while true do
		swait()
		if hitted == false and dis.Parent ~= nil then
			PixelBlock(3,math.random(0,2),"Add",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),5,5,5,0.05,BrickColor.new("Lime green"),0)
			trl.Color = ColorSequence.new(BrickColor.new("Earth green").Color)
			efec.Color = ColorSequence.new(BrickColor.new("Lime green").Color)
coroutine.resume(coroutine.create(function()
for i, v in pairs(FindNearestHead(dis.CFrame.p, 50)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
end))
		elseif hitted == true and dis.Parent == nil then
			break
		end
	end
end))
coroutine.resume(coroutine.create(function()
dis.Touched:connect(function(hit) 
	if hitted == false and hit.Parent ~= char then
	hitted = true
	shakes(1,1)
	efec.Enabled = false
	for i = 0, 3 do
	CFuncs["Sound"].Create("rbxassetid://1368637781", dis, 7.5,1)
CFuncs["Sound"].Create("rbxassetid://763718160", dis, 10, 1.1)
CFuncs["Sound"].Create("rbxassetid://782353443", dis, 10, 1)
CFuncs["Sound"].Create("rbxassetid://335657174", dis, 10, 1)
end
MagniDamage(dis, 125, 82000,345700005, 0, "Normal")
coroutine.resume(coroutine.create(function()
for i, v in pairs(FindNearestHead(dis.CFrame.p, 100)) do
if v:FindFirstChild('Head') then
dmg(v)
end
end
end))
	sphere2(2,"Add",dis.CFrame,vt(1,1,1),3,3,3,BrickColor.new("Earth green"),keptcolor.Color)
	sphere2(3,"Add",dis.CFrame,vt(1,1,1),3,3,3,BrickColor.new("Earth green"),keptcolor.Color)
	sphere2(4,"Add",dis.CFrame,vt(1,1,1),4,4,4,BrickColor.new("Lime green"),keptcolor.Color)
	sphere2(5,"Add",dis.CFrame,vt(1,1,1),4,4,4,BrickColor.new("Earth green"),keptcolor.Color)
	coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2344870656"
eff.LightEmission = 1
eff.Color = ColorSequence.new(dis.Color)
eff.Rate = 10000000
eff.Enabled = true
eff.EmissionDirection = "Front"
eff.Lifetime = NumberRange.new(3)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,75,0),NumberSequenceKeypoint.new(0.1,20,0),NumberSequenceKeypoint.new(0.8,40,0),NumberSequenceKeypoint.new(1,60,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0.8,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(350)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.SpreadAngle = Vector2.new(0,900)
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.2)
eff.Enabled = false
	end))
	coroutine.resume(coroutine.create(function()
		for i = 0, 9 do
local disr = CreateParta(char,1,1,"Neon",keptcolor)
disr.CFrame = dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",disr)
at1.Position = vt(-30,0,0)
local at2 = Instance.new("Attachment",disr)
at2.Position = vt(30,0,0)
local trl = Instance.new('Trail',disr)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://2342682798"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(BrickColor.new(0,MRANDOM(0,1),0).Color)
trl.Lifetime = 0.5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = disr.CFrame.lookVector*math.random(150,350)
bv.Parent = disr
local val = 0
coroutine.resume(coroutine.create(function()
	swait(30)
	for i = 0, 9 do
		swait()
		val = val + 0.1
		trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, val),NumberSequenceKeypoint.new(1, 1)})
	end
game:GetService("Debris"):AddItem(disr, 3)
end))
end
local eff = Instance.new("ParticleEmitter",dis)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.new(0,MRANDOM(0,1),0).Color)
eff.Rate = 500000
eff.Lifetime = NumberRange.new(0.5,2)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,5,0),NumberSequenceKeypoint.new(0.8,4,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(100,400)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.25)
eff.Enabled = false
end))
	for i = 0, 19 do
		slash(math.random(10,20)/10,5,true,"Round","Add","Out",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(200,800)/250,BrickColor.new("Really black"))
	end
for i = 0, 49 do
PixelBlock(1,math.random(5,40),"Add",dis.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),10,10,10,0.2,BrickColor.new("Lime green"),0)
end
coroutine.resume(coroutine.create(function()
for i = 0, 19 do
swait()
hum.CameraOffset = vt(math.random(-10,10)/70,math.random(-10,10)/70,math.random(-10,10)/70)
end
hum.CameraOffset = vt(0,0,0)
end))
dis.Anchored = true
dis.Transparency = 1
wait(8)
dis:Destroy()
end
end)
end))
for i = 0,2,0.1 do
		swait()
              RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0,-0.3,-0.5)* angles(math.rad(30),math.rad(0),math.rad(0)),0.3)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(10),math.rad(0),math.rad(0)),.3)
RW.C0 = clerp(RW.C0, CFrame.new(1.25, 0.5, -0.5) * angles(math.rad(40), math.rad(0), math.rad(-10)), 0.3)
LW.C0 = clerp(LW.C0, CFrame.new(-1.25, 0.5, -0.5) * angles(math.rad(40), math.rad(0), math.rad(10)), 0.3)
RH.C0=clerp(RH.C0,cf(1,-0.75 - 0.05 * math.cos(sine / 25),-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1.5),math.rad(0),math.rad(30)),.3)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 25),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(10)),.3)
	end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function lovesqueal()
attack = true
hum.WalkSpeed = 0
CFuncs["Sound"].Create("rbxassetid://2500548008", root, 2.5, 1)
local blush = Instance.new("Decal",hed)
blush.Texture = "rbxassetid://898404027"
blush.Face = "Front"
for i = 0, 11, 0.1 do
	swait()
	RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(3),math.rad(0),math.rad(20 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(6),math.rad(0),math.rad(-20 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.2 + 0.02 * math.cos(sine / 32),0 + 0.05 * math.cos(sine / 32))*angles(math.rad(20 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-20 - 5 * math.cos(sine / 37)),math.rad(0 + 14 * math.cos(sine / 58)),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(3 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-3 - 3 * math.cos(sine / 45))),.1)
end
for x = 0, 1 do
for i = 0, 1, 0.2 do
	swait()
	RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(3),math.rad(0),math.rad(-5 - 2 * math.cos(sine / 32))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(6),math.rad(0),math.rad(5 + 2 * math.cos(sine / 32))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.05 + 0.02 * math.cos(sine / 32),0 + 0.05 * math.cos(sine / 32))*angles(math.rad(-5 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-5 - 5 * math.cos(sine / 37)),math.rad(0 + 14 * math.cos(sine / 58)),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(3 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.75 + 0.025 * math.cos(sine / 45),-0.6)*angles(math.rad(140 - 3 * math.cos(sine / 73)),math.rad(5 - 1 * math.cos(sine / 55)),math.rad(80 - 3 * math.cos(sine / 45))),.3)
end
for i = 0, 1, 0.2 do
	swait()
	RH.C0=clerp(RH.C0,cf(1,-1.025 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(3),math.rad(0),math.rad(-5 - 2 * math.cos(sine / 32))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1.025 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(6),math.rad(0),math.rad(5 + 2 * math.cos(sine / 32))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.05 + 0.02 * math.cos(sine / 32),0.025 + 0.05 * math.cos(sine / 32))*angles(math.rad(-5 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-5 - 5 * math.cos(sine / 37)),math.rad(0 + 14 * math.cos(sine / 58)),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(3 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.8 + 0.025 * math.cos(sine / 45),-0.6)*angles(math.rad(140 - 3 * math.cos(sine / 73)),math.rad(5 - 1 * math.cos(sine / 55)),math.rad(80 - 3 * math.cos(sine / 45))),.3)
end
end
coroutine.resume(coroutine.create(function()
for i = 0, 49 do
swait()
blush.Transparency = blush.Transparency + 0.02
end
blush:Destroy()
end))
attack = false
hum.WalkSpeed = storehumanoidWS	
end

function shytaunty()
attack = true
hum.WalkSpeed = 0
CFuncs["Sound"].Create("rbxassetid://543623779", char, 3, 1)
for i = 0, 13, 0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28) + 0.05 * math.cos(sine / 44),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(7 - 5 * math.cos(sine / 44)),math.rad(0),math.rad(-6 - 3 * math.cos(sine / 34))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28) - 0.05 * math.cos(sine / 44),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(3 + 5 * math.cos(sine / 44)),math.rad(0),math.rad(0 + 3 * math.cos(sine / 34))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.05 * math.cos(sine / 44),0 + 0.03 * math.cos(sine / 34),-0.05 + 0.05 * math.cos(sine / 28))*angles(math.rad(0 - 3 * math.cos(sine / 34)),math.rad(0 - 5 * math.cos(sine / 44)),math.rad(-5)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(2 - 2.5 * math.cos(sine / 28)),math.rad(20 + 5 * math.cos(sine / 62)),math.rad(35 + 5 * math.cos(sine / 59))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.1 * math.cos(sine / 28),-0.45)*angles(math.rad(22 - 1 * math.cos(sine / 53)),math.rad(0),math.rad(-60 + 2 * math.cos(sine / 37))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.1 * math.cos(sine / 28),-0.45)*angles(math.rad(26 - 2 * math.cos(sine / 58)),math.rad(0),math.rad(59 - 3 * math.cos(sine / 57) )),.1)
end
hum.WalkSpeed = storehumanoidWS
attack = false
end

function EndLess_Power()
attack = true
hum.WalkSpeed = 0
ModeOfGlitch = 0
MAINRUINCOLOR = BrickColor.new("Bright orange")
newThemeCust("rbxassetid://899090278",0,1.01,1.75)
local vel = Instance.new("BodyPosition", root)
vel.P = 10000
vel.D = 1000
vel.maxForce = Vector3.new(50000000000, 10e10, 50000000000)
vel.position = root.CFrame.p + vt(0,250,0)
CFuncs["Sound"].Create("rbxassetid://1295446488", char, 5, 0.5)
CFuncs["Sound"].Create("rbxassetid://1368598393", char, 7.5, 0.5)
for i = 0, 49 do
slash(math.random(10,100)/10,3,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-10,10)),math.rad(math.random(-360,360)),math.rad(math.random(-10,10))),vt(0.05,0.01,0.05),math.random(25,500)/250,BrickColor.new("White"))
end
for i = 0, 5, 0.1 do
swait()
CamShakeAll(5,15,char)
RH.C0=clerp(RH.C0,cf(1,-0.05,-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.5,-0.25)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,1.5 + 0.1 * math.cos(sine / 28))*angles(math.rad(20 - 1 * math.cos(sine / 34)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(-20 + 2.5 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(20 - 2.5 * math.cos(sine / 28))),.1)
end
local efec = Instance.new("ParticleEmitter",root)
efec.Texture = "rbxassetid://0"
efec.LightEmission = 1
efec.Color = ColorSequence.new(Color3.new(math.random(0.6,1),0,0))
efec.Rate = 5
efec.Lifetime = NumberRange.new(3)
efec.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,15,0),NumberSequenceKeypoint.new(0.2,40,0),NumberSequenceKeypoint.new(0.6,150,0),NumberSequenceKeypoint.new(0.8,75,0),NumberSequenceKeypoint.new(1,25,0)})
efec.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.25,0),NumberSequenceKeypoint.new(0.6,0.25,0),NumberSequenceKeypoint.new(1,1,0)})
efec.Drag = 5
efec.LockedToPart = true
efec.Rotation = NumberRange.new(-500,500)
efec.VelocitySpread = 9000
efec.RotSpeed = NumberRange.new(-500,500)
local absval = 0
local efec2 = efec:Clone()
efec2.LightEmission = 1
efec2.Texture = "rbxassetid://2092248396"
efec2.Parent = root
efec2.Rate = 10
efec2.Lifetime = NumberRange.new(2)
efec2.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,175,0),NumberSequenceKeypoint.new(0.5,150,0),NumberSequenceKeypoint.new(0.8,500,0),NumberSequenceKeypoint.new(1,1500*absval/3,0)})
efec2.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.5,0.5,0),NumberSequenceKeypoint.new(1,1,0)})
efec2.Speed = NumberRange.new(0)
efec2.RotSpeed = NumberRange.new(-100,100)
local efec3 = efec:Clone()
efec3.LightEmission = 1
efec3.Color = ColorSequence.new(Color3.new(math.random(0.6,1),0,0))
efec3.Texture = "rbxassetid://2273224484"
efec3.Parent = root
efec3.Rate = 10000
efec3.Drag = 5
efec3.LockedToPart = false
efec3.Lifetime = NumberRange.new(2)
efec3.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,5,0),NumberSequenceKeypoint.new(0.5,10,0),NumberSequenceKeypoint.new(0.8,15,0),NumberSequenceKeypoint.new(1,0,0)})
efec3.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.5,0,0),NumberSequenceKeypoint.new(1,1,0)})
efec3.Speed = NumberRange.new(50,1550)
efec3.RotSpeed = NumberRange.new(-100,100)
CFuncs["Sound"].Create("rbxassetid://1368583274", char, 7.5, 0.25)
local WaitHere = 0
repeat
swait()
WaitHere = WaitHere + 0.05
CamShakeAll(5,15,char)
absval = absval + 0.006
efec2.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,175*absval/3,0),NumberSequenceKeypoint.new(0.5,150*absval/3,0),NumberSequenceKeypoint.new(0.8,500*absval/3,0),NumberSequenceKeypoint.new(1,1500*absval/3,0)})
if math.random(1,2) == 1 then
slash(math.random(50,100)/10,2,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(absval/3.25,0.01,absval/3.25),math.random(50,100)/500,BrickColor.new(math.random(0,1),0,0))
end
if math.random(1,5) == 1 then
slash(math.random(10,100)/10,2,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.075,0.01,0.075),absval/3,BrickColor.new(math.random(0,1),0,0))
end
sphere2(4,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(6.25,1.25,6.25),-0.15,absval*2.5,-0.15,BrickColor.new(math.random(0,1),0,0))
RH.C0=clerp(RH.C0,cf(1,-0.05,-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.5,-0.25)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,1.5 + 0.1 * math.cos(sine / 28))*angles(math.rad(20 - 1 * math.cos(sine / 34)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(-20 + 2.5 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(20 - 2.5 * math.cos(sine / 28))),.1)
until WaitHere > 19
for i = 0, 17.5, 0.1 do
swait()
CamShakeAll(5,17.5,char)
if math.random(1,2) == 1 then
slash(math.random(50,100)/10,2,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(absval/3,0.01,absval/3),math.random(50,100)/500,BrickColor.new(math.random(0,1),0,0))
end
if math.random(1,4) == 1 then
slash(math.random(10,100)/10,2,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.15,0.01,0.15),absval/3,BrickColor.new(math.random(0,1),0,0))
end
sphere2(4,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(12.5,2.5,12.5),-0.15,absval*2.5,-0.15,BrickColor.new(math.random(0,1),0,0))
RH.C0=clerp(RH.C0,cf(1,-0.05,-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.5,-0.25)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,1.5 + 0.1 * math.cos(sine / 28))*angles(math.rad(20 - 1 * math.cos(sine / 34)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(-20 + 2.5 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(20 - 2.5 * math.cos(sine / 28))),.1)
end
               ModeOfGlitch = 765688533321
storehumanoidWS = 260
hum.WalkSpeed = 260
rainbowmode = false
unstablemode = true
chaosmode = false
CRAZED = false
RecolorTextAndRename("UNSTABLE",Color3.new(1,1,1),Color3.new(1,0,0),"Arcade")
MAINRUINCOLOR = BrickColor.new("Really black")
RecolorThing(BrickColor.new("Institutional white"),BrickColor.new("Really red"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Crimson"),0,BrickColor.new("Really black"),true,true)
disably = false
warnedpeople("UNSTABLE POWER!!","Arcade",BrickColor.new("Really red").Color,BrickColor.new("White").Color)
disably = true
efec.Enabled = false
efec2.Enabled = false
efec3.Enabled = false
CFuncs["Sound"].Create("rbxassetid://1368637781", char, 5, 0.25)
CFuncs["Sound"].Create("rbxassetid://1368637781", char, 5, 0.5)
CFuncs["Sound"].Create("rbxassetid://1368637781", char, 5, 0.75)
CFuncs["Sound"].Create("rbxassetid://1368637781", char, 7.5, 1)
CFuncs["Sound"].Create("rbxassetid://1368605755", char, 7.5, 1)
CFuncs["Sound"].Create("rbxassetid://763718160", char, 10, 0.5)
CFuncs["Sound"].Create("rbxassetid://763718160", char, 10, 0.25)
CFuncs["Sound"].Create("rbxassetid://782353443", char, 10, 1)
CFuncs["Sound"].Create("rbxassetid://782353443", char, 10, 0.75)
CFuncs["LongSound"].Create("rbxassetid://782353443", char, 10, 0.5)
CFuncs["LongSound"].Create("rbxassetid://782353443", char, 10, 0.25)
for i = 0, 2 do
CFuncs["Sound"].Create("rbxassetid://763717897", char, 10, 0.5)
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 10, 1)
end
for i = 0, 20 do
local dis = CreateParta(char,1,1,"Neon",BrickColor.new(math.random(0,1),0,0))
dis.CFrame = root.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-25000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(25000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(BrickColor.new(math.random(0,1),0,0).Color)
trl.Lifetime = 5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 15)
end
for i = 0, 10 do
sphere2(1,"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(10,10,10),-0.1,absval*100,-0.1,BrickColor.new(math.random(0,1),0,0))
end
for i = 0, 9, 0.1 do
swait()
shakes(2.1,0.2)

for i = 0, 4 do
slash(math.random(10,50)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(absval/2,0.01,absval/2),math.random(50,5000)/100,BrickColor.new(math.random(0,1),0,0))
end
RH.C0=clerp(RH.C0,cf(1,-0.05,-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.5,-0.25)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,1.5 + 0.1 * math.cos(sine / 28))*angles(math.rad(20 - 1 * math.cos(sine / 34)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(-20 + 2.5 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(20 - 2.5 * math.cos(sine / 28))),.1)
end
vel:Destroy()
efec:Destroy()
efec2:Destroy()
efec3:Destroy()
hum.WalkSpeed = 200
attack = false
end

function PowerBeams()
attack = true
hum.WalkSpeed = 0 
CFuncs["Sound"].Create("rbxassetid://159882644", root, 15, 1)
CFuncs["EchoSound"].Create("rbxassetid://159882644", char, 1.8, 1,0.1,10,0.15,0.5,1)
local radm = math.random(1,3)
if radm == 1 then
bosschatfunc("YOU WONT STAY FOR TOO LONG.",MAINRUINCOLOR.Color,2)
elseif radm == 2 then
bosschatfunc("HOW MANY MINUTE WILL YOU LAST?!",MAINRUINCOLOR.Color,2)
elseif radm == 3 then
bosschatfunc("YOUR DEATH IS HERE!",MAINRUINCOLOR.Color,2)
end
local keptcolor = BrickColor.new(math.random(0.35,1),0,0)
coroutine.resume(coroutine.create(function()
wait(1.25)
CamShakeAll(30,35,char)
for i = 0, 13 do
swait(6)
local orb = Instance.new("Part", char)
CFuncs["Sound"].Create("rbxassetid://663361028", char, 0.8, 1)
        orb.BrickColor = BrickColor.new(math.random(0.35,1),0,0)
        orb.CanCollide = false
        orb.FormFactor = 3
        orb.Name = "Ring"
        orb.Material = "Neon"
        orb.Size = Vector3.new(1, 1, 1)
        orb.Transparency = 0
        orb.TopSurface = 0
        orb.BottomSurface = 0
orb.Anchored = true
        local orbm = Instance.new("SpecialMesh", orb)
        orbm.MeshType = "Sphere"
orbm.Name = "SizeMesh"
orbm.Scale = vt(5,5,5)
orb.CFrame = root.CFrame*CFrame.new(math.random(-90,90),math.random(45,85),math.random(-90,90))
sphere2(6,"Add",orb.CFrame,vt(1.25,1.25,1.25),0.025,0.025,0.025,BrickColor.new(math.random(0.35,1),0,0))
for i = 0, 4 do
slash(math.random(10,20)/10,5,true,"Round","Add","Out",orb.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(10,50)/250,BrickColor.new("White"))
end
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",orb)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.new(math.random(0.35,1),0,0).Color)
eff.Rate = 1500
eff.Lifetime = NumberRange.new(0.5,1)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,4,0),NumberSequenceKeypoint.new(0.2,1,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.2,0,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(10,30)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-500,500)
wait(0.25)
eff.Enabled = false
end))
coroutine.resume(coroutine.create(function()
wait(1)
CFuncs["Sound"].Create("rbxassetid://161006182", char, 0.8, 1.1)
sphere2(3,"Add",orb.CFrame,vt(5,5,5),0.025,0.025,0.025,BrickColor.new(math.random(0.35,1),0,0))
sphere2(4,"Add",orb.CFrame,vt(5,5,5),0.025,0.025,0.025,BrickColor.new(math.random(0.35,1),0,0))
orb.Transparency = 1
	local a = Instance.new("Part",char)
	a.Name = "Direction"	
	a.Anchored = true
	a.BrickColor = BrickColor.new(math.random(0.35,1),0,0)
a.Material = "Neon"
a.Transparency = 0.25
a.Shape = "Cylinder"
	local ht = Instance.new("Part",char)
	ht.Name = "DirectionHit"	
	ht.Anchored = true
	ht.BrickColor = BrickColor.new(math.random(0.35,1),0,0)
ht.CanCollide = false
ht.Transparency = 1
ht.Size = vt(0.1,0.1,0.1)
CFuncs["Sound"].Create("rbxassetid://183763487", char, 0.95, 1.2)
CFuncs["Sound"].Create("rbxassetid://183763487", ht, 5, 1.2)
	a.CanCollide = false
	local ray = Ray.new(
	    orb.CFrame.p,                           -- origin
	    (mouse.Hit.p - orb.CFrame.p).unit * 500 -- direction
	) 
	local ignore = char
	local hit, position, normal = workspace:FindPartOnRay(ray, ignore)
	a.BottomSurface = 10
	a.TopSurface = 10
	local distance = (orb.CFrame.p - position).magnitude
	a.Size = Vector3.new(distance,1,1)
	a.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance/2)
	ht.CFrame = CFrame.new(orb.CFrame.p, position) * CFrame.new(0, 0, -distance)
sphere2(2,"Add",ht.CFrame,vt(20,20,20),0.15,0.15,0.15,BrickColor.new(math.random(0.35,1),0,0))
sphere2(4,"Add",ht.CFrame,vt(20,20,20),0.15,0.15,0.15,BrickColor.new(math.random(0.35,1),0,0))
MagniDamage(ht, 17.5, 65,99, 0, "Normal")
CamShakeAll(15,5,char)
coroutine.resume(coroutine.create(function()
	for i = 0, 9 do
local disr = CreateParta(char,1,1,"Neon",BrickColor.new(math.random(0.35,1),0,0))
disr.CFrame = ht.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",disr)
at1.Position = vt(-2,0,0)
local at2 = Instance.new("Attachment",disr)
at2.Position = vt(2,0,0)
local trl = Instance.new('Trail',disr)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://2325530138"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(BrickColor.new(math.random(0.35,1),0,0).Color)
trl.Lifetime = 0.5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = disr.CFrame.lookVector*math.random(25,100)
bv.Parent = disr
local val = 0
coroutine.resume(coroutine.create(function()
	swait(20)
	for i = 0, 9 do
		swait()
		val = val + 0.1
		trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, val),NumberSequenceKeypoint.new(1, 1)})
	end
game:GetService("Debris"):AddItem(disr, 3)
end))
end
local eff = Instance.new("ParticleEmitter",ht)
eff.Texture = "rbxassetid://2273224484"
eff.LightEmission = 1
eff.Color = ColorSequence.new(BrickColor.new(math.random(0.35,1),0,0).Color)
eff.Rate = 5000
eff.Lifetime = NumberRange.new(0.5,1.5)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,30,0),NumberSequenceKeypoint.new(0.2,2,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.5,0.25,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(5,100)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
wait(0.25)
eff.Enabled = false
end))
for i = 0, 4 do
slash(math.random(10,60)/10,5,true,"Round","Add","Out",ht.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(10,75)/125,BrickColor.new("White"))
sphere2(8,"Add",orb.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(6,6,6),-0.005,0.125,-0.005,BrickColor.new(math.random(0.35,1),0,0))
sphere2(4,"Add",ht.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(4,2,4),-0.01,0.5,-0.01,BrickColor.new(math.random(0.35,1),0,0))
local rsiz = math.random(10,30)
sphereMK(math.random(2,4),0.25,"Add",ht.CFrame*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),rsiz/3,rsiz/3,rsiz/3,0,BrickColor.new(math.random(0.35,1),0,0),0)
end
a.CFrame = a.CFrame*CFrame.Angles(0,math.rad(90),0)
local msh = Instance.new("SpecialMesh",a)
msh.MeshType = "Cylinder"
msh.Scale = vt(1,2.5,2.5)
for i = 0, 49 do
swait()
msh.Scale = msh.Scale + vt(0,0.01,0.01)
a.Transparency = a.Transparency + 0.02
end
wait(1)
orb:Destroy()
a:Destroy()
ht:Destroy()
end))
game:GetService("Debris"):AddItem(orb, 10)
end
end))
	for i = 0,14,0.1 do
		swait()
sphere2(7,"Add",sorb.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.075,-0.01,MAINRUINCOLOR)
local snap = math.random(1,12)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),1)
end
sphere2(8,"Add",rleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Really red"),BrickColor.new("Really red").Color)
sphere2(8,"Add",lleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Really black"),BrickColor.new("Really black").Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 50),0 + 0.25 * math.cos(sine / 43),6 + 1 * math.cos(sine / 32))*angles(math.rad(-13 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(40)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-23 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-40 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(170 + 6 * math.cos(sine / 72)),math.rad(2 - 4 * math.cos(sine / 58)),math.rad(35 + 1 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(6 - 5 * math.cos(sine / 59)),math.rad(73 - 3 * math.cos(sine / 45))),.1)
	end
hum.WalkSpeed = storehumanoidWS
attack = false
end

function Unstable_Taunt()
attack = true
hum.WalkSpeed = 0
chatfunc("I don't have all day..",Color3.new(0,0,0),"Inverted","Antique",0.75)
CFuncs["Sound"].Create("rbxassetid://159882303", root, 25, 1)
CFuncs["Sound"].Create("rbxassetid://159882303", char, 4.5, 1)
for i = 0,9,0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 50),0 + 0.25 * math.cos(sine / 43),6 + 1 * math.cos(sine / 32))*angles(math.rad(-3 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 15 * math.cos(sine / 6)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-40 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(170 + 6 * math.cos(sine / 72)),math.rad(2 - 4 * math.cos(sine / 58)),math.rad(-12.5 + 1 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(6 - 5 * math.cos(sine / 59)),math.rad(73 - 3 * math.cos(sine / 45))),.1)
end
attack = false
hum.WalkSpeed = storehumanoidWS
end

function smiter()
local targetted = nil
if mouse.Target.Parent ~= Character and mouse.Target.Parent.Parent ~= Character and mouse.Target.Parent:FindFirstChildOfClass("Humanoid") ~= nil then
targetted = mouse.Target.Parent
end
if targetted ~= nil then
attack = true
hum.WalkSpeed = 0
coroutine.resume(coroutine.create(function()
CFuncs["Sound"].Create("rbxassetid://1117054464", targetted.Head, 2, 1)
sphere2(4,"Add",targetted.Head.CFrame,vt(8,8,8),0.1,0.1,0.1,MAINRUINCOLOR)
local vel = Instance.new("BodyPosition", targetted.Head)
vel.P = 12500
vel.D = 1000
vel.maxForce = Vector3.new(50000000000, 10e10, 50000000000)
vel.position = targetted.Head.CFrame.p
end))
CFuncs["Sound"].Create("rbxassetid://671759140", sorb2, 1, 1.2)
	for i = 0,4,0.1 do
		swait()
sphere2(4,"Add",sorb2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.125,-0.01,MAINRUINCOLOR)
            RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0,0,0)* angles(math.rad(0),math.rad(0),math.rad(-60)),0.2)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(-10),math.rad(0),math.rad(60)),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.01 * math.cos(sine / 28),0)*angles(math.rad(15),math.rad(15),math.rad(-10)),.2)
LW.C0=clerp(LW.C0, CFrame.new(-1.5, 0.5, 0) * angles(math.rad(170), math.rad(0), math.rad(-40)), 0.2)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1.5),math.rad(0),math.rad(0)),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(20),math.rad(5)),.2)
	end
coroutine.resume(coroutine.create(function()
CamShakeAll(30,35,char)
MagniDamage(targetted.Head, 18, 18,30, 0, "Normal")
CFuncs["Sound"].Create("rbxassetid://1042705869", targetted.Head, 6.5, 0.8)
CFuncs["Sound"].Create("rbxassetid://1042716828", targetted.Head, 6.25, 0.8)
CFuncs["Sound"].Create("rbxassetid://1117054464", targetted.Head, 5, 0.8)
for i = 0, 19 do
slash(math.random(10,50)/10,5,true,"Round","Add","Out",targetted.Head.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(50,250)/250,BrickColor.new("White"))
end
sphere2(3,"Add",targetted.Head.CFrame,vt(0,40000,0),0.25,0,0.25,MAINRUINCOLOR)
sphere2(2,"Add",targetted.Head.CFrame,vt(0,40000,0),0.25,0,0.25,MAINRUINCOLOR)
sphere2(4,"Add",targetted.Head.CFrame,vt(0,0,0),0.5,0.5,0.5,MAINRUINCOLOR)
sphere2(5,"Add",targetted.Head.CFrame,vt(0,0,0),0.5,0.5,0.5,MAINRUINCOLOR)
coroutine.resume(coroutine.create(function()
local eff = Instance.new("ParticleEmitter",targetted.Head)
eff.Texture = "rbxassetid://363275192"
eff.LightEmission = 0.95
eff.Color = ColorSequence.new(MAINRUINCOLOR.Color)
eff.Rate = 10000
eff.Lifetime = NumberRange.new(1.5)
eff.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,15,0),NumberSequenceKeypoint.new(0.8,25,0),NumberSequenceKeypoint.new(1,0,0)})
eff.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.8,0.5,0),NumberSequenceKeypoint.new(1,1,0)})
eff.Speed = NumberRange.new(25,150)
eff.Drag = 5
eff.Rotation = NumberRange.new(-500,500)
eff.VelocitySpread = 9000
eff.RotSpeed = NumberRange.new(-50,50)
local eff2 = eff:Clone()
eff2.Parent = targetted.Head
eff2.LightEmission = 1
eff2.Color = ColorSequence.new(Color3.new(0.75,0.5,1))
eff2.Texture = "rbxassetid://2273224484"
eff2.Rate = 10000
eff2.Lifetime = NumberRange.new(1,3)
eff2.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,20,0),NumberSequenceKeypoint.new(0.2,10,0),NumberSequenceKeypoint.new(1,0,0)})
eff2.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.2,0,0),NumberSequenceKeypoint.new(0.8,0.5,0),NumberSequenceKeypoint.new(1,1,0)})
eff2.Drag = 5
eff2.Speed = NumberRange.new(50,250)
eff2.Rotation = NumberRange.new(-500,500)
eff2.VelocitySpread = 9000
wait(0.5)
eff2.Enabled = false
eff.Enabled = false
end))
for i = 0, 9 do
sphere2(3,"Add",targetted.Head.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(2,1,2),-0.02,3,-0.02,MAINRUINCOLOR)
end
for i = 0, 49 do
local rsiz = math.random(10,50)
sphereMK(math.random(1,4),1,"Add",targetted.Head.CFrame*CFrame.new(math.random(-20,20)/50,math.random(-20,20)/50,math.random(-20,20)/50)*CFrame.Angles(math.rad(90 + math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),rsiz/10,rsiz/10,rsiz/10,0,MAINRUINCOLOR,0)
end
game:GetService("Debris"):AddItem(vel,1)
dmg(targetted)
end))
	for i = 0,1,0.1 do
		swait()
            RootJoint.C0 = clerp(RootJoint.C0,RootCF*cf(0,0,0)* angles(math.rad(0),math.rad(0),math.rad(-70)),0.5)
Torso.Neck.C0 = clerp(Torso.Neck.C0,necko *angles(math.rad(5),math.rad(0),math.rad(70)),.5)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.01 * math.cos(sine / 28),0)*angles(math.rad(15),math.rad(15),math.rad(-10)),.5)
LW.C0=clerp(LW.C0, CFrame.new(-1.5, 0.5, 0) * angles(math.rad(40), math.rad(0), math.rad(-50)), 0.5)
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1.5),math.rad(0),math.rad(0)),.5)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1),math.rad(20),math.rad(5)),.5)
	end
attack = false
hum.WalkSpeed = storehumanoidWS
end
end

local ast = {187744332,1426044282}
local CardT = {"LET'S PLAY SOME CARD SHALL WE?","WANNA SEE SOME MAGIC?","YOU'RE GETTING TRICKY!!","NOW MY TURN.."}
local IDTECC = {"MEAN WHILE...","DO YOU REMEMBER ME?","COME OUT..","ROCK 'N ROLL!"}
-------------------------------------

pcall(function()
Humanoid.Animator.Parent = nil
char.Animate.Parent = nil end)

-------------------------------------

	local NewInstance = function(instance,parent,properties)
		local inst = Instance.new(instance,parent)
		if(properties)then
			for i,v in next, properties do
				pcall(function() inst[i] = v end)
			end
		end
		return inst;
	end

if script.MeshValue.Value == "true" then

local Core = script.newCORE
Core.Parent = Character
coroutine.resume(coroutine.create(function()
	while true do
		swait()
		if rainbowmode == false and Error == false and CRAZED == false then
		for i,v in pairs(Core:GetChildren())do
			if v.Name ~= "CORE2" then
				v.BrickColor = MAINRUINCOLOR
			end
		end
		elseif Error == true then
		for i,v in pairs(Core:GetChildren())do
			if v.Name ~= "CORE2" then
				v.BrickColor = BrickColor.Random()
			end
		end
		elseif CRAZED == true then
		for i,v in pairs(Core:GetChildren())do
			if v.Name ~= "CORE2" then
				v.BrickColor = BrickColor.new(0,0,1)
			end
		end
		swait(5)
		for i,v in pairs(Core:GetChildren())do
			if v.Name ~= "CORE2" then
				v.BrickColor = BrickColor.new(0,0,0)
			end
		end
		elseif rainbowmode == true then
		for i,v in pairs(Core:GetChildren())do
			if v.Name ~= "CORE2" then
				v.Color = Color3.new(r/255,g/255,b/255)
			end
		end
     end
  end
end))
for i,v in pairs(Core:GetChildren())do
if v.Name ~= "Weld" then
v.Transparency = 1
end
end
local HWz = NewInstance('Weld',Character,{Part0 = tors, Part1 = Core.Weld, C0 = CFrame.new(0,0,-0.35) * CFrame.Angles(-99,-100,0)})
end


local attacktype = 1
mouse.Button1Down:connect(function()
  if attack == false and attacktype == 1 then
    attacktype = 2
    attackone()
  elseif attack == false and attacktype == 2 then
    attacktype = 1
    attacktwo()
  end
end)
mouse.KeyDown:connect(function(k)
if Diversial == false then
if k == "q" and attack == false and ModeOfGlitch ~= 1 then
--normalmog() ---Disabled due to crashing... only in VSB
ModeOfGlitch = 1
storehumanoidWS = 16
hum.WalkSpeed = 16
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
newTheme("rbxassetid://415898123",48.6,1,1.25)
RecolorTextAndRename("MAYHEM",Color3.new(0.25,0,0),Color3.new(1,0,0),"Antique")
MAINRUINCOLOR = BrickColor.new("Really red")
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
end
if k == "b" and attack == false and ModeOfGlitch == 1 and ModeOfGlitch ~= 453453484635345 then
               ModeOfGlitch = 453453484635345
storehumanoidWS = 25
hum.WalkSpeed = 25
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("DEATH",Color3.new(0.2,0.2,0.2),Color3.new(0,0,0),"Bodoni")
newTheme("rbxassetid://318062766",0,1.01,0.85)
MAINRUINCOLOR = BrickColor.new("Really black")
chatfunc("Death is not an escape..",MAINRUINCOLOR.Color,"Inverted","Arcade",1.2)
RecolorThing(MAINRUINCOLOR,BrickColor.new("Dark stone grey"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "e" and attack == false and ModeOfGlitch ~= 2 then
ModeOfGlitch = 2
storehumanoidWS = 16
hum.WalkSpeed = 16
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("PURITY",Color3.new(0,1,1),Color3.new(1,1,1),"Code")
newTheme("rbxassetid://1119453744",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("Toothpaste")
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "r" and attack == false and ModeOfGlitch ~= 3 then
               ModeOfGlitch = 3
storehumanoidWS = 16
hum.WalkSpeed = 16
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("CORRUPTION",Color3.new(0,0,0),Color3.new(0.35,0,1),"Antique")
newTheme("rbxassetid://1283869370",58.15,0.98,1.25)
MAINRUINCOLOR = BrickColor.new("Royal purple")
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "b" and attack == false and ModeOfGlitch == 3 then
               ModeOfGlitch = 3434
storehumanoidWS = 100
hum.WalkSpeed = 100
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("UNKNOWN",Color3.new(1,1,1),BrickColor.new("New Yeller").Color,"Code")
newTheme("rbxassetid://1861780345",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("New Yeller")
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0.8,MAINRUINCOLOR,0.8,MAINRUINCOLOR,true,true)
end
if k == "m" and attack == false and ModeOfGlitch == 3 then
attack = true
ModeOfGlitch = 259394695439876
hum.WalkSpeed = 0
newThemeCust("rbxassetid://190845741",9,1,1.25)
wait(2)
for i = 0, 15, 0.1 do
		swait()
RH.C0=clerp(RH.C0,cf(1, -1 - 0.025 * math.cos(sine/12), -0.01)*angles(math.rad(0),math.rad(83),math.rad(0))*angles(math.rad(0),math.rad(0),math.rad(0)),0.1)
LH.C0=clerp(LH.C0,cf(-1, -1 - 0.05 * math.cos(sine/12), -0.01)*angles(math.rad(0),math.rad(-83),math.rad(0))*angles(math.rad(0),math.rad(0),math.rad(0)),0.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0, 0, 0 + 0.05 * math.cos(sine / 12))*angles(math.rad(0),math.rad(0),math.rad(0)),0.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*cf(0, 0, 0 + ((1) - 1))*angles(math.rad(15 - 2.5 * math.sin(sine / 12)),math.rad(0),math.rad(0)),0.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.125 * math.cos(sine / 12),-0.45)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.125 * math.cos(sine / 12),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
end
CamShakeAll(25,90,char)
sphere(2,"Add",root.CFrame,vt(0,0,0),12.5,MAINRUINCOLOR)
sphere(3,"Add",root.CFrame,vt(0,0,0),10,MAINRUINCOLOR)
sphere(1,"Add",root.CFrame,vt(0,0,0),7.5,MAINRUINCOLOR)
sphere(2,"Add",root.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(3,"Add",root.CFrame,vt(0,0,0),2.5,MAINRUINCOLOR)
CFuncs["Sound"].Create("rbxassetid://847061203", char, 2,1)
               ModeOfGlitch = 146536
storehumanoidWS = 12
hum.WalkSpeed = 12
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("VANTA-X",Color3.new(0.15,0.15,0.15),Color3.new(0.35,0,1),"Fantasy")
MAINRUINCOLOR = BrickColor.new("Bright violet")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Dark indigo"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
attack = false
end
if k == "t" and attack == false and ModeOfGlitch ~= 4 then
               ModeOfGlitch = 4
storehumanoidWS = 16
hum.WalkSpeed = 16
rainbowmode = false
unstablemode = false
chaosmode = true
CRAZED = false
RecolorTextAndRename("CHAOS",Color3.new(0,0,0),Color3.new(1,1,1),"Arcade")
newTheme("rbxassetid://1369263130",0,1.01,1.25)
MAINRUINCOLOR = BrickColor.new("Black")
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "m" and attack == false and ModeOfGlitch == 4 and ModeOfGlitch ~= 102341 then
TheoriesTran()
end
if k == "y" and attack == false and ModeOfGlitch ~= 5 then
               ModeOfGlitch = 5
storehumanoidWS = 16
hum.WalkSpeed = 16
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("DIVINITY",Color3.new(1,1,1),Color3.new(1,1,0.5),"SciFi")
newTheme("rbxassetid://661079869",0,1.02,1.25)
MAINRUINCOLOR = BrickColor.new("Bright yellow")
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "u" and attack == false and ModeOfGlitch ~= 6 then
               ModeOfGlitch = 6
storehumanoidWS = 100
hum.WalkSpeed = 100
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("EQUALITY",Color3.new(0,0,0),Color3.new(1,1,1),"Fantasy")
newTheme("rbxassetid://1347011178",0,1.01,1.25)
MAINRUINCOLOR = BrickColor.new("White")
RecolorThing(MAINRUINCOLOR,BrickColor.new("White"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "f" and attack == false and ModeOfGlitch ~= 8 then
               ModeOfGlitch = 8
storehumanoidWS = 140
hum.WalkSpeed = 140
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("DESTINY",Color3.new(1,1,1),BrickColor.new("Alder").Color,"Code")
newThemeCust("rbxassetid://1495032271",0,1.01,1.25)
MAINRUINCOLOR = BrickColor.new("Alder")
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "g" and attack == false and ModeOfGlitch ~= 9 then
               ModeOfGlitch = 9
storehumanoidWS = 150
hum.WalkSpeed = 150
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("INFECTION X",Color3.new(0,1,0),Color3.new(0,0.7,0),"Bodoni")
newTheme("rbxassetid://798163149",0,1,1.4)
MAINRUINCOLOR = BrickColor.new("Camo")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Camo"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "n" and attack == false and ModeOfGlitch == 9 and ModeOfGlitch ~= 103 then
               ModeOfGlitch = 103
storehumanoidWS = 200
hum.WalkSpeed = 200
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
blush.Transparency = 0
RecolorTextAndRename("ACE-OF-SPADES",BrickColor.new("Really black").Color,BrickColor.new("New Yeller").Color,"SciFi")
newTheme("rbxassetid://1986375341",0,1.02,1.2)
MAINRUINCOLOR = BrickColor.new("New Yeller")
disably = false
warnedpeople(CardT[math.random(1,4)],"SciFi",BrickColor.new("Really black").Color,BrickColor.new("New Yeller").Color)
disably = true
RecolorThing(MAINRUINCOLOR,BrickColor.new("Really black"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Deep orange"),0,BrickColor.new("White"),true,true)
end
if k == "m" and attack == false and ModeOfGlitch == 102 and ModeOfGlitch ~= 1236 then
               ModeOfGlitch = 1236
storehumanoidWS = 16
hum.WalkSpeed = 16
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
blush.Transparency = 0
RecolorTextAndRename("Loost :>",BrickColor.new("Hot pink").Color,BrickColor.new("Carnation pink").Color,"SciFi")
newTheme("rbxassetid://736003449",0,1.07,2)
MAINRUINCOLOR = BrickColor.new("Hot pink")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Carnation pink"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,BrickColor.new("Really blue"),1,BrickColor.new("Hot pink"),true,false)
end
if k == "m" and attack == false and ModeOfGlitch == 9 and ModeOfGlitch ~= 6518594185 then
               ModeOfGlitch = 6518594185
storehumanoidWS = 200
hum.WalkSpeed = 200
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
blush.Transparency = 0
RecolorTextAndRename("IDOLS",BrickColor.new("Hot pink").Color,BrickColor.new("New Yeller").Color,"SciFi")
newTheme("rbxassetid://2415462372",0,1.02,1.2)
MAINRUINCOLOR = BrickColor.new("New Yeller")
disably = false
IdolsWarn(IDTECC[math.random(1,4)],"SciFi",BrickColor.new("Hot pink").Color,BrickColor.new("New Yeller").Color)
disably = true
RecolorThing(MAINRUINCOLOR,BrickColor.new("Deep orange"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Really blue"),0,BrickColor.new("Hot pink"),true,true)
end
if k == "n" and attack == false and ModeOfGlitch == 8889 and ModeOfGlitch ~= 808080808080808080808080 then
               ModeOfGlitch = 808080808080808080808080
storehumanoidWS = 250
hum.WalkSpeed = 250
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
CFuncs["Sound"].Create("rbxassetid://763717897", char, 4, 0.75)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 8, 0.5)
CFuncs["Sound"].Create("rbxassetid://1192402877", char, 10, 0.5)
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 6, 0.5)
RecolorTextAndRename("MYTHICAL",BrickColor.new("Dark indigo").Color,BrickColor.new("Really blue").Color,"Bodoni")
newThemeCust("rbxassetid://398455752",0,1.01,2)
disably = false
warnedpeople("SYNTH ONBOUND!!","Arcade",BrickColor.new("Alder").Color,BrickColor.new("Pastel light blue").Color)
disably = true
MAINRUINCOLOR = BrickColor.new("Really blue")
bosschatfunc("Are you gonna stop now or what?",MAINRUINCOLOR.Color,1)
RecolorThing(MAINRUINCOLOR,BrickColor.new("Dark indigo"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Alder"),0,BrickColor.new("Pastel light blue"),true,true)
end
if k == "b" and attack == false and ModeOfGlitch == 8889 and ModeOfGlitch ~= 88893333388 then
attack = true
hum.WalkSpeed = 0
--7.725
newThemeCust("rbxassetid://1504604335",0,1.01,1.5)
bosschatfunc("I have been waiting for so long.",MAINRUINCOLOR.Color,10)
coroutine.resume(coroutine.create(function()
local locat = Instance.new("Part", char)
locat.CanCollide = false
locat.FormFactor = 3
locat.Name = "Ring"
locat.Material = "Neon"
locat.Size = Vector3.new(1, 1, 1)
locat.Transparency = 1
locat.TopSurface = 0
locat.BottomSurface = 0
locat.Anchored = true
locat.CFrame = root.CFrame*CFrame.new(0,-3,0)
local poste = 0
local rotation = 0
local upperpos = 0
local rate = 0
local x = locat
for i = 0, 38.5, 0.1 do
swait()
local rsiz = math.random(150,450)
local rsiz2 = math.random(10,45)
sphere2(math.random(1,2),"Add",x.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))*CFrame.new(math.random(-200,200),math.random(-200,200),math.random(-200,200)),vt(1,1,1),-0.01,math.random(50,250)/10,-0.01,BrickColor.new("Royal purple"))
sphereMK(1,4,"Add",tors.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),rsiz2/2,rsiz2/2,rsiz2/2,0,MAINRUINCOLOR,-200)	
end
end))
for i = 0, 47, 0.1 do
		swait()
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 5 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),0.925 + 0.15 * math.cos(sine / 32))*angles(math.rad(20 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.35,0.5 + 0.025 * math.cos(sine / 45),-0.3)*angles(math.rad(145 + 3 * math.cos(sine / 79)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 6 * math.cos(sine / 73))),.1)
LW.C0=clerp(LW.C0,cf(-1.35,0.5 + 0.025 * math.cos(sine / 45),-0.3)*angles(math.rad(150 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(27 - 6 * math.cos(sine / 33))),.1)
end
bosschatfunc("This won't be more easier now.",MAINRUINCOLOR.Color,10)
for i = 0, 3, 0.1 do
		swait()
sphere2(8,"Add",sorb2.CFrame*CFrame.new(0,0,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Royal purple"),BrickColor.new("Royal purple").Color)
sphere2(8,"Add",sorb.CFrame*CFrame.new(0,0,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Really blue"),BrickColor.new("Really blue").Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 5 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),0.925 + 0.15 * math.cos(sine / 32))*angles(math.rad(10 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.2,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(50 + 7 * math.cos(sine / 79)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-43 + 10 * math.cos(sine / 73))),.1)
LW.C0=clerp(LW.C0,cf(-1.2,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(65 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(47 - 6 * math.cos(sine / 33))),.1)
end
for i = 0, 3, 0.1 do
		swait()
sphere2(8,"Add",sorb2.CFrame*CFrame.new(0,0,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Royal purple"),BrickColor.new("Royal purple").Color)
sphere2(8,"Add",sorb.CFrame*CFrame.new(0,0,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,0.05,-0.01,BrickColor.new("Really blue"),BrickColor.new("Really blue").Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 5 * math.cos(sine / 51))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 44))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1.2 + 0.15 * math.cos(sine / 32))*angles(math.rad(-15.5 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.2)
RW.C0=clerp(RW.C0,cf(1,0.6 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(155 + 7 * math.cos(sine / 79)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-43 + 10 * math.cos(sine / 73))),.2)
LW.C0=clerp(LW.C0,cf(-1,0.6 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(160 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(57 - 6 * math.cos(sine / 33))),.2)
end
coroutine.resume(coroutine.create(function()
local locat = Instance.new("Part", char)
locat.CanCollide = false
locat.FormFactor = 3
locat.Name = "Ring"
locat.Material = "Neon"
locat.Size = Vector3.new(1, 1, 1)
locat.Transparency = 1
locat.TopSurface = 0
locat.BottomSurface = 0
locat.Anchored = true
locat.CFrame = root.CFrame*CFrame.new(0,-3,0)
local poste = 0
local rotation = 0
local upperpos = 0
local rate = 0
local x = locat
local rsiz = math.random(150,450)
local rsiz2 = math.random(10,45)
for i = 0, 99 do
slash(math.random(10,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(250,2500)/250,BrickColor.new("White"))
end
for i = 0, 49 do
rsiz = math.random(150,450)
sphere2(math.random(1,4),"Add",tors.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(15,1,15),-0.05,math.random(25,500)/25,-0.05,BrickColor.new("Royal purple"))
end
end))
for i = 0, 55 do
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = root.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-25000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(25000,0,0)
local trl = Instance.new('Trail',dis)
trl.Attachment0 = at1
trl.FaceCamera = true
trl.Attachment1 = at2
trl.Texture = "rbxassetid://1049219073"
trl.LightEmission = 1
trl.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0),NumberSequenceKeypoint.new(1, 1)})
trl.Color = ColorSequence.new(MAINRUINCOLOR.Color)
trl.Lifetime = 5
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 10)
end
               ModeOfGlitch = 88893333388
storehumanoidWS = 200
hum.WalkSpeed = 200
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("CATASTROPHE",BrickColor.new("Royal purple").Color,BrickColor.new("Really blue").Color,"Bodoni")
bosschatfunc("Evenly with your rejection.",MAINRUINCOLOR.Color,1)
MAINRUINCOLOR = BrickColor.new("Royal purple")
CFuncs["Sound"].Create("rbxassetid://419447292", char, 4, 1)
sphere2(2,"Add",root.CFrame*CFrame.new(0,0,0),vt(3,3,3),1.8*2,1.8*2,1.8*2,MAINRUINCOLOR)
sphere2(4,"Add",root.CFrame*CFrame.new(0,0,0),vt(3,3,3),1.8*2,1.8*2,1.8*2,MAINRUINCOLOR)
sphere2(6,"Add",root.CFrame*CFrame.new(0,0,0),vt(1.5,0.5,1.5),20,0,20,BrickColor.new("Lilac"))
sphere2(4,"Add",root.CFrame*CFrame.new(0,0,0),vt(1.5,0.5,1.5),20,0,20,BrickColor.new("Really blue"))
sphere2(2,"Add",root.CFrame*CFrame.new(0,0,0),vt(1.5,0.5,1.5),20,0,20,MAINRUINCOLOR)
sphere2(6,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),1.8*2,7,1.8*2,BrickColor.new("Really blue"))
sphere2(5,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),1.6*2,7,1.6*2,BrickColor.new("Royal purple"))
sphere2(4,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),1.2*2,7,1.2*2,BrickColor.new("Really blue"))
sphere2(3,"Add",root.CFrame*CFrame.Angles(0,0,0),vt(1,10000,1),1*2,7,1*2,BrickColor.new("Royal purple"))
coroutine.resume(coroutine.create(function()
for i = 0, 5, 0.1 do
		swait()
shakes(2.1,0.2)
end
end))
local effx = Instance.new("ParticleEmitter",root)
effx.Texture = "rbxassetid://144580273" -- 144580273 74564879
effx.LightEmission = 1
effx.Color = ColorSequence.new(BrickColor.new("Royal purple").Color)
effx.Rate = 500000
effx.Lifetime = NumberRange.new(0.25,0.75)
effx.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(1,200,0)})
effx.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1,0),NumberSequenceKeypoint.new(0.1,0.5,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx.Speed = NumberRange.new(0,10)
effx.Drag = 5
effx.Rotation = NumberRange.new(-500,500)
effx.VelocitySpread = 9000
effx.RotSpeed = NumberRange.new(-50,50)
local effx2 = Instance.new("ParticleEmitter",root)
effx2.Texture = "rbxassetid://2273224484"
effx2.LightEmission = 1
effx2.Color = ColorSequence.new(BrickColor.new("Royal purple").Color)
effx2.Rate = 500000
effx2.Lifetime = NumberRange.new(1,2)
effx2.Size = NumberSequence.new({NumberSequenceKeypoint.new(0,200,0),NumberSequenceKeypoint.new(0.1,50,0),NumberSequenceKeypoint.new(0.8,25,0),NumberSequenceKeypoint.new(1,0,0)})
effx2.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,0,0),NumberSequenceKeypoint.new(0.1,0,0),NumberSequenceKeypoint.new(0.8,0,0),NumberSequenceKeypoint.new(1,1,0)})
effx2.Speed = NumberRange.new(50,1200)
effx2.Drag = 5
effx2.Rotation = NumberRange.new(-500,500)
effx2.VelocitySpread = 9000
effx2.RotSpeed = NumberRange.new(-50,50)
local effx3 = effx2:Clone()
effx3.Parent = root
effx3.Color = ColorSequence.new(BrickColor.new("Really blue").Color)
symbolizeBlink(root,0,144580273,BrickColor.new("Royal purple").Color,40,0,0,0,root,true,-5,2)
symbolizeBlink(root,0,144580273,BrickColor.new("Really blue").Color,50,0,0,0,root,true,-5,1)
coroutine.resume(coroutine.create(function()
wait(0.3)
effx.Enabled = false
effx2.Enabled = false
effx3.Enabled = false
end))
for i = 0, 1 do
CFuncs["Sound"].Create("rbxassetid://763717897", char, 5, 1.25)
CFuncs["Sound"].Create("rbxassetid://1192402877", char, 5,0.75)
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 2.5,1)
CFuncs["Sound"].Create("rbxassetid://763718160", char, 5, 0.75)
end
RecolorThing(MAINRUINCOLOR,BrickColor.new("Really blue"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Navy blue"),0,BrickColor.new("Dark indigo"),true,true)
for i = 0, 3, 0.1 do
		swait()
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 5 * math.cos(sine / 51))),.3)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 44))),.3)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(35.5 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.3)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.3)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(-35 + 7 * math.cos(sine / 79)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(33 + 10 * math.cos(sine / 73))),.3)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(-22 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-27 - 6 * math.cos(sine / 33))),.3)
end
attack = false
end
if k == "m" and attack == false and ModeOfGlitch == 8 and ModeOfGlitch ~= 8889 then
               ModeOfGlitch = 8889
storehumanoidWS = 180
hum.WalkSpeed = 180
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("CALAMITY",BrickColor.new("Alder").Color,BrickColor.new("Lilac").Color,"Antique")
newThemeCust("rbxassetid://1359036559",11.7,1.01,1.5)
MAINRUINCOLOR = BrickColor.new("Lilac")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Alder"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "m" and attack == false and ModeOfGlitch == 1 and ModeOfGlitch ~= 664663666 then
newThemeCust("rbxassetid://723652641",0,1,1.25)
attack = true
hum.WalkSpeed = 0
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
chatfunc("I'm really done with you..",MAINRUINCOLOR.Color,"Inverted","Arcade",3)
MAINRUINCOLOR = BrickColor.new("Maroon")
local keptcolor = MAINRUINCOLOR
local locat = Instance.new("Part", char)
locat.CanCollide = false
locat.FormFactor = 3
locat.Name = "Ring"
locat.Material = "Neon"
locat.Size = Vector3.new(1, 1, 1)
locat.Transparency = 1
locat.TopSurface = 0
locat.BottomSurface = 0
locat.Anchored = true
locat.CFrame = root.CFrame*CFrame.new(0,-3,0)
local poste = 0
local rotation = 0
local upperpos = 0
local rate = 0
local x = locat
for i = 0, 24, 0.1 do
swait()
slash(math.random(50,100)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(5,50)/250,BrickColor.new("White"))
sphereMK(1,-2,"Add",tors.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2.5,2.5,15,-0.025,MAINRUINCOLOR,100)
RH.C0=clerp(RH.C0,cf(1,-0.05,-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.5,-0.25)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,1.5 + 0.1 * math.cos(sine / 28))*angles(math.rad(20 - 1 * math.cos(sine / 34)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(-20 + 2.5 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(20 - 2.5 * math.cos(sine / 28))),.1)
end
coroutine.resume(coroutine.create(function()
CamShakeAll(25,30,char)
sphere(5,"Add",root.CFrame,vt(0,0,0),2.5,MAINRUINCOLOR)
CFuncs["Sound"].Create("rbxassetid://847061203", char, 0.5,1)
wait(0.55)
CamShakeAll(25,60,char)
sphere(5,"Add",root.CFrame,vt(0,0,0),7.5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),2.5,MAINRUINCOLOR)
CFuncs["Sound"].Create("rbxassetid://847061203", char, 1,1)
wait(0.55)
CamShakeAll(25,90,char)
sphere(5,"Add",root.CFrame,vt(0,0,0),12.5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),10,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),7.5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),2.5,MAINRUINCOLOR)
CFuncs["Sound"].Create("rbxassetid://847061203", char, 2,1)
wait(0.55)
CamShakeAll(60,120,char)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 4, 1)
CFuncs["Sound"].Create("rbxassetid://1192402877", char, 2.5, 0.75)
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 4, 0.95)
sphere2(1,"Add",x.CFrame*CFrame.new(0,0,0),vt(15,0,15),5,0,5,BrickColor.new("Really black"))
sphere2(2,"Add",x.CFrame*CFrame.new(0,0,0),vt(15,0,15),5,0,5,keptcolor)
sphere2(1,"Add",x.CFrame*CFrame.new(0,0,0),vt(5,50000,5),1.5,1,1.5,BrickColor.new("Maroon"))
sphere2(2,"Add",x.CFrame*CFrame.new(0,0,0),vt(5,50000,5),1.5,1,1.5,BrickColor.new("Really black"))
sphere2(4,"Add",x.CFrame*CFrame.new(0,0,0),vt(5,50000,5),1.5,1,1.5,keptcolor)
coroutine.resume(coroutine.create(function()
for i = 0, 99 do
local dis = CreateParta(char,1,1,"Neon",MAINRUINCOLOR)
dis.CFrame = root.CFrame*CFrame.new(math.random(-5,5),math.random(-5,5),math.random(-5,5))*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360)))
local at1 = Instance.new("Attachment",dis)
at1.Position = vt(-25000,0,0)
local at2 = Instance.new("Attachment",dis)
at2.Position = vt(25000,0,0)
local bv = Instance.new("BodyVelocity")
bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
bv.velocity = dis.CFrame.lookVector*math.random(500,2500)
bv.Parent = dis
game:GetService("Debris"):AddItem(dis, 10)
end
attack = false
hum.WalkSpeed = storehumanoidWS
for i = 0, 99 do
--slash(math.random(10,30)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(0.01,0.01,0.01),math.random(250,2500)/250,BrickColor.new("Maroon"))
end
for i = 0, 49 do
local rsiz = math.random(150,450)
end
end))
end))
for i = 0, 12.5, 0.1 do
swait()
RH.C0=clerp(RH.C0,cf(1,-0.05,-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(-30)),.1)
LH.C0=clerp(LH.C0,cf(-1,-0.5,-0.25)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(30)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,1.5 + 0.1 * math.cos(sine / 28))*angles(math.rad(20 - 1 * math.cos(sine / 34)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(55),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(-20 + 2.5 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-0.75,0.5,-0.25)*angles(math.rad(140),math.rad(0),math.rad(20 - 2.5 * math.cos(sine / 28))),.1)
end
      ModeOfGlitch = 664663666 
storehumanoidWS = 175
hum.WalkSpeed = 175
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("STRESSED",Color3.new(0.1,0,0),Color3.new(0.25,0,0),"Antique")
MAINRUINCOLOR = BrickColor.new("Maroon")
chatfunc("YOU'RE EXISTING IS WASTED!!",MAINRUINCOLOR.Color,"Inverted","Arcade",3)
RecolorThing(MAINRUINCOLOR,BrickColor.new("Really black"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
attack = false
hum.WalkSpeed = storehumanoidWS
end
if k == "m" and attack == false and ModeOfGlitch == 6 then
               ModeOfGlitch = 3444
storehumanoidWS = 100
hum.WalkSpeed = 100
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("TWISTED",Color3.new(1,1,1),BrickColor.new("Storm blue").Color,"Code")
newTheme("rbxassetid://919231299",0,1,1)
MAINRUINCOLOR = BrickColor.new("Storm blue")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Storm blue"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "n" and attack == false and ModeOfGlitch == 6 and ModeOfGlitch ~= 765688533321 then
EndLess_Power()
end
if k == "n" and attack == false and ModeOfGlitch == 1 and ModeOfGlitch ~= 55469696922 then
               ModeOfGlitch = 55469696922
storehumanoidWS = 275
hum.WalkSpeed = 275
rainbowmode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("NATURE",Color3.new(1,1,1),BrickColor.new("Forest green").Color,"Code")
newTheme("rbxassetid://181761264",0,1,1.4)
MAINRUINCOLOR = BrickColor.new("Forest green")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Forest green"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "n" and attack == false and ModeOfGlitch == 2 and ModeOfGlitch ~= 4367677813 then
               ModeOfGlitch = 4367677813
storehumanoidWS = 225
hum.WalkSpeed = 225
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("SHD",Color3.new(0.75,0.9,1),BrickColor.new("Pink").Color,"Arcade")
newTheme("rbxassetid://363284685",0,1.01,1.25)
MAINRUINCOLOR = BrickColor.new("Baby blue")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Pink"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "b" and attack == false and ModeOfGlitch == 9999999921111 and ModeOfGlitch ~= 101 then
               ModeOfGlitch = 101
storehumanoidWS = 350
hum.WalkSpeed = 350
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("ALPHA",BrickColor.new("Black").Color,BrickColor.new("Storm blue").Color,"SciFi")
disably = false
warnedpeople("A L P H A.","Arcade",BrickColor.new("Black").Color,BrickColor.new("Storm blue").Color)
disably = true
newTheme("rbxassetid://1280010741",0,1.01,1.8)
MAINRUINCOLOR = BrickColor.new("Storm blue")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Black"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Bright bluish green"),0,BrickColor.new("Really black"),true,true)
end
if k == "n" and attack == false and ModeOfGlitch == 8 and ModeOfGlitch ~= 9999999921111 then
               ModeOfGlitch = 9999999921111
storehumanoidWS = 300
hum.WalkSpeed = 300
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("OMEGA",BrickColor.new("Really black").Color,BrickColor.new("Bright bluish green").Color,"SciFi")
newTheme("rbxassetid://643309199",0,1.01,1.5)
MAINRUINCOLOR = BrickColor.new("Bright bluish green")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Really black"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "h" and attack == false and ModeOfGlitch ~= 102 then
               ModeOfGlitch = 102
storehumanoidWS = 18
hum.WalkSpeed = 18
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("U-K-E",BrickColor.new("White").Color,BrickColor.new("Deep orange").Color,"Code")
newTheme("rbxassetid://1426044282",0,1,1.15)
MAINRUINCOLOR = BrickColor.new("Deep orange")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Pastel orange"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
end
if k == "l" and attack == false and ModeOfGlitch ~= 343434 then
rainbowmode = false
unstablemode = false
sphere(0.9,"Add",root.CFrame,vt(0,100000,0),1,BrickColor.new("Neon orange"))
for i = 0, 49 do
PixelBlock(1,math.random(1,20),"Add",root.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),8,8,8,0.16,BrickColor.new("CGA brown"),0)
end
ModeOfGlitch = 343434
storehumanoidWS = 16
hum.WalkSpeed = 16
chaosmode = false
CRAZED = false
RecolorTextAndRename("FIRE-FLARES",BrickColor.new("Gold").Color,BrickColor.new("Neon orange").Color,"Antique")
newTheme("rbxassetid://1259692095",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("CGA brown")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Neon orange"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "m" and attack == false and ModeOfGlitch == 343434 then
               ModeOfGlitch = 343435
storehumanoidWS = 125
hum.WalkSpeed = 125
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("FALLENNIGHT",BrickColor.new("Navy blue").Color,BrickColor.new("Really blue").Color,"SciFi")
newTheme("rbxassetid://561833161",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("Really blue")
bosschatfunc("Night have been fallen..",MAINRUINCOLOR.Color,0.9)
RecolorThing(MAINRUINCOLOR,BrickColor.new("Navy blue"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Dark blue"),0,BrickColor.new("Deep blue"),true,true)
end
if k == "n" and attack == false and ModeOfGlitch == 999 then
               ModeOfGlitch = 1055
storehumanoidWS = 50
hum.WalkSpeed = 50
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("PANDORA",BrickColor.new("White").Color,BrickColor.new("Lavender").Color,"Code")
newTheme("rbxassetid://1382488262",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("Lavender")
RecolorThing(MAINRUINCOLOR,BrickColor.new("White"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
end
if k == "k" and attack == false and ModeOfGlitch ~= 999 then
               ModeOfGlitch = 999
storehumanoidWS = 34
hum.WalkSpeed = 34
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("ENBELEIVED",Color3.new(1,1,1),Color3.new(1,1,1),"SciFi")
newTheme("rbxassetid://286050652",0,1,1)
MAINRUINCOLOR = BrickColor.new("Cloudy grey")
RecolorThing(MAINRUINCOLOR,BrickColor.new("Cloudy grey"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "j" and attack == false and ModeOfGlitch ~= 090 then
               ModeOfGlitch = 090
storehumanoidWS = 250
hum.WalkSpeed = 250
rainbowmode = true
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("RAINBOW",Color3.new(0.5,1,1),BrickColor.new("Really red").Color,"Antique")
newTheme("rbxassetid://1747430851",0,1,1.15)
MAINRUINCOLOR = BrickColor.new("Pastel green")
RecolorThing(BrickColor.new("Deep orange"),BrickColor.new("Toothpaste"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,BrickColor.new("Deep orange"),true,false)
end
if k == "m" and attack == false and ModeOfGlitch == 090 then
               ModeOfGlitch = 909090
storehumanoidWS = 250
hum.WalkSpeed = 250
rainbowmode = true
unstablemode = false
chaosmode = false
CRAZED = false
disably = false
warnedpeople("Let's relax..","Highway",Color3.new(1,1,1),Color3.new(1,1,1))
disably = true
RecolorTextAndRename("AESTHETIC",Color3.new(0.5,1,1),BrickColor.new("Really red").Color,"Antique")
newTheme("rbxassetid://1416617454",0,1,1.15)
MAINRUINCOLOR = BrickColor.new("Pastel green")
RecolorThing(BrickColor.new("Deep orange"),BrickColor.new("Toothpaste"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,BrickColor.new("Deep orange"),true,false)
end
if k == "n" and attack == false and ModeOfGlitch == 4 and ModeOfGlitch ~= 999999999556 then
               ModeOfGlitch = 999999999556
storehumanoidWS = 500
hum.WalkSpeed = 500
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = true
RecolorTextAndRename("CRAZED",BrickColor.new("Really black").Color,BrickColor.new("Navy blue").Color,"Code")
newTheme("rbxassetid://719008519",0,1.02,1.25)
MAINRUINCOLOR = BrickColor.new("Navy blue")
bosschatfunc("HAHAHAHAHAHA!!!",MAINRUINCOLOR.Color,3)
RecolorThing(MAINRUINCOLOR,BrickColor.new("Really black"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Navy blue"),0,BrickColor.new("Really blue"),true,true)
end
if k == "n" and attack == false and ModeOfGlitch == 5 and ModeOfGlitch ~= 1264532489 then
newThemeCust("rbxassetid://1505487022",0,1.01,1.7)
attack = true
hum.WalkSpeed = 0
chatfunc("The justice will never be fallen..",MAINRUINCOLOR.Color,"Inverted","Arcade",5)
local keptcolor = MAINRUINCOLOR
local locat = Instance.new("Part", char)
locat.CanCollide = false
locat.FormFactor = 3
locat.Name = "Ring"
locat.Material = "Neon"
locat.Size = Vector3.new(1, 1, 1)
locat.Transparency = 1
locat.TopSurface = 0
locat.BottomSurface = 0
locat.Anchored = true
locat.CFrame = root.CFrame*CFrame.new(0,-3,0)
local poste = 0
local rotation = 0
local upperpos = 0
local rate = 0
local x = locat
coroutine.resume(coroutine.create(function()
wait(1.2)
repeat
wait(0.175)
until math.random(1,50) == 1
end))
repeat
swait()
sphereMK(1,-2,"Add",sorb2.CFrame*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),2.5,2.5,15,-0.025,MAINRUINCOLOR,100)
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),-0.1 + 0.05 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-10)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-35 - 1 * math.cos(sine / 28)),math.rad(0 + 10 * math.cos(sine / 79)),math.rad(25 + 4 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-2 - 4 * math.cos(sine / 28)),math.rad(-20),math.rad(18 + 8 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(170 + 3 * math.cos(sine / 46)),math.rad(10 + 5 * math.cos(sine / 52)),math.rad(-10 - 2 * math.cos(sine / 28))),.1)
until math.random(1,50) == 1
coroutine.resume(coroutine.create(function()
CamShakeAll(25,60,char)
chatfunc("You are..",MAINRUINCOLOR.Color,"Inverted","Arcade",1)
sphere(5,"Add",root.CFrame,vt(0,0,0),7.5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),2.5,MAINRUINCOLOR)
CFuncs["Sound"].Create("rbxassetid://847061203", char, 1,1)
wait(0.55)
chatfunc("JUST A DIRTY BAD GUY!!!",MAINRUINCOLOR.Color,"Inverted","Arcade",3)
CamShakeAll(25,90,char)
sphere(5,"Add",root.CFrame,vt(0,0,0),12.5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),10,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),7.5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),5,MAINRUINCOLOR)
sphere(5,"Add",root.CFrame,vt(0,0,0),2.5,MAINRUINCOLOR)
CFuncs["Sound"].Create("rbxassetid://847061203", char, 2,1)
wait(0.55)
               ModeOfGlitch = 1264532489
storehumanoidWS = 250
hum.WalkSpeed = 250
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("FALLENX",Color3.new(0.5,1,1),BrickColor.new("Deep orange").Color,"Antique")
MAINRUINCOLOR = BrickColor.new("Pastel green")
bosschatfunc("WHO'S DARE TO CHALLENGE ME?!",MAINRUINCOLOR.Color,3)
RecolorThing(BrickColor.new("Deep orange"),BrickColor.new("Toothpaste"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,MAINRUINCOLOR,0,BrickColor.new("Deep orange"),true,true)
CamShakeAll(60,120,char)
CFuncs["Sound"].Create("rbxassetid://763717897", char, 4, 1)
CFuncs["Sound"].Create("rbxassetid://1192402877", char, 2.5, 0.75)
CFuncs["Sound"].Create("rbxassetid://1664711478", char, 4, 0.95)
tbeam(BrickColor.new("Deep orange"),BrickColor.new("Toothpaste"))
end))
for i = 0, 9, 0.1 do
swait()
slash(math.random(50,100)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(5,50)/250,BrickColor.new("White"))
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(2),math.rad(0),math.rad(-16 + 4 * math.cos(sine / 34))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1.5),math.rad(10),math.rad(11 + 2 * math.cos(sine / 34))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,1 + 0.1 * math.cos(sine / 28))*angles(math.rad(-6 - 3 * math.cos(sine / 34)),math.rad(0),math.rad(-25 - 4 * math.cos(sine / 53))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 1 * math.cos(sine / 28)),math.rad(0 + 10 * math.cos(sine / 79)),math.rad(25 + 4 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-2 - 4 * math.cos(sine / 28)),math.rad(-20),math.rad(18 + 8 * math.cos(sine / 28))),.1)
end
attack = false
end

elseif Diversial == true then
-- Diversial Glitcher Started

if k == "q" and attack == false and ModeOfGlitch ~= 676767 then
               ModeOfGlitch = 676767
storehumanoidWS = 250
hum.WalkSpeed = 250
rainbowmode = false
chaosmode = false
CRAZED = false
Error = false
RecolorTextAndRename("MULTI-DIVERSIAL",Color3.new(1,1,1),BrickColor.new("Royal purple").Color,"SciFi")
newTheme("rbxassetid://603567552",0,1.02,1.25)
MAINRUINCOLOR = BrickColor.new("Royal purple")
RecolorThing2(BrickColor.new("Royal purple"),BrickColor.new("Royal purple"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,MAINRUINCOLOR,0,BrickColor.new("Royal purple"),true,true)
end
if k == "g" and attack == false and ModeOfGlitch ~= 71 then
               ModeOfGlitch = 71
storehumanoidWS = 12
hum.WalkSpeed = 12
rainbowmode = false
chaosmode = false
Error = false
CRAZED = false
RecolorTextAndRename("PLAGUES",Color3.new(0,0.3,0),BrickColor.new("Lime green").Color,"Code")
newTheme("rbxassetid://577543579",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("Lime green")
chatfunc("The plague is already inside..",MAINRUINCOLOR.Color,"Inverted","Arcade",1.7)
RecolorThing2(MAINRUINCOLOR,BrickColor.new("Earth green"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,false)
end
if k == "e" and attack == false and ModeOfGlitch ~= 2332 then
               ModeOfGlitch = 2332
storehumanoidWS = 12
hum.WalkSpeed = 12
rainbowmode = false
chaosmode = false
Error = false
CRAZED = false
RecolorTextAndRename("NANNIIH",Color3.new(1,1,1),BrickColor.new("Pastel Blue").Color,"Fantasy")
newTheme("rbxassetid://2482117221",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("Pastel Blue")
RecolorThing2(MAINRUINCOLOR,BrickColor.new("Pastel Blue"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
end
if k == "r" and attack == false and ModeOfGlitch ~= 2334 then
               ModeOfGlitch = 2334
storehumanoidWS = 12
hum.WalkSpeed = 12
rainbowmode = false
chaosmode = false
Error = false
CRAZED = false
RecolorTextAndRename("MEMER",BrickColor.new'Toothpaste'.Color,BrickColor.new'Dark blue'.Color,"Fantasy")
newTheme("rbxassetid://1702473314",0,1,0.3)
MAINRUINCOLOR = BrickColor.new("Dark blue")
RecolorThing2(MAINRUINCOLOR,BrickColor.new("Pastel Blue"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
end
if k == "f" and attack == false and ModeOfGlitch ~= 666666 then

               ModeOfGlitch = 666666
storehumanoidWS = 85
hum.WalkSpeed = 85
rainbowmode = false
chaosmode = false
Error = false
CRAZED = false
RecolorTextAndRename("L O S T  S O U L",BrickColor.new("Crimson").Color,BrickColor.new("Really black").Color,"Arcade")
newTheme("rbxassetid://343860759",0,0.9,2)
MAINRUINCOLOR = BrickColor.new("Crimson")
bosschatfunc("I'  M  T H E  U N D Y I N G  S O U L .",MAINRUINCOLOR.Color,3)
RecolorThing2(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,BrickColor.new("Crimson"),0,BrickColor.new("Crimson"),true,false)
end
if k == "t" and attack == false and ModeOfGlitch ~= 61 then
               ModeOfGlitch = 61
storehumanoidWS = 850
hum.WalkSpeed = 850
rainbowmode = false
chaosmode = false
CRAZED = false
Error = true
RecolorTextAndRename("error",Color3.new(0,0,0),Color3.new(1,1,1),"Bodoni")
disably = false
warnedpeople("ERROR","Arcade",MAINRUINCOLOR.Color,MAINRUINCOLOR.Color)
disably = true
chatfunc("AGHHEUHUF#H(HHGDCVHH*I%H#$@($UF*GD",MAINRUINCOLOR.Color,"Inverted","Arcade",2)
newTheme("rbxassetid://1138145518",0,1,1.25)
MAINRUINCOLOR = BrickColor.new("Black")
bosschatfunc("THIS CAN BE DELETED!!!",MAINRUINCOLOR.Color,3)
RecolorThing(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,true,true)
end
end
if k == "p" and Diversial == false and attack == false then
Diversial = true
ModeFrame.Text = "Side: Diversial"
TextFrame.Text = "Diversial Glitcher"
ModeOfGlitch = 676767
storehumanoidWS = 250
hum.WalkSpeed = 250
rainbowmode = false
unstablemode = false
chaosmode = false
CRAZED = false
RecolorTextAndRename("MULTI-DIVERSIAL",Color3.new(1,1,1),BrickColor.new("Royal purple").Color,"SciFi")
newTheme("rbxassetid://603567552",0,1.02,1.25)
MAINRUINCOLOR = BrickColor.new("Royal purple")
RecolorThing2(BrickColor.new("Royal purple"),BrickColor.new("Royal purple"),MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,0,MAINRUINCOLOR,0,BrickColor.new("Royal purple"),true,true)
elseif k == "p" and Diversial == true and attack == false then
Error = false
Diversial = false
TextFrame.Text = "Spectrum Glitcher"
ModeFrame.Text = "Side: Spectrum"
--normalmog() ---Disabled due to crashing... only in VSB
ModeOfGlitch = 1
storehumanoidWS = 16
hum.WalkSpeed = 16
rainbowmode = false
chaosmode = false
CRAZED = false
newTheme("rbxassetid://415898123",48.6,1,1.25)
RecolorTextAndRename("MAYHEM",Color3.new(0.25,0,0),Color3.new(1,0,0),"Antique")
MAINRUINCOLOR = BrickColor.new("Really red")
RecolorThing2(MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,MAINRUINCOLOR,1,MAINRUINCOLOR,1,MAINRUINCOLOR,false,false)
end
if k == "0" then
if ClickToFling == true and FlingOnTouchh == false then
ClickToFling = false
FlingOnTouchh = true
DamageFrame.Text = "Fling = Touch"
elseif ClickToFling == false and FlingOnTouchh == true then
ClickToFling = true
FlingOnTouchh = false
DamageFrame.Text = "Fling = Click"
end
end

if k == "z" and attack == false and ModeOfGlitch == 1 then
ExtinctiveHeartbreak()
elseif k == "z" and attack == false and ModeOfGlitch == 2 then
HeavenlyDisk()
elseif k == "z" and attack == false and ModeOfGlitch == 71 then
PlaguedJump()
elseif k == "z" and attack == false and ModeOfGlitch == 8889 then
CalMets()
elseif k == "z" and attack == false and ModeOfGlitch == 88893333388 then
CatMets()
elseif k == "z" and attack == false and ModeOfGlitch == 9 then
EternalChaosOrb()
elseif k == "z" and attack == false and ModeOfGlitch == 2332 then
ClearDisk()
elseif k == "b" and attack == false and ModeOfGlitch == 999 then
ColorEnbelived()
elseif k == "c" and attack == false and ModeOfGlitch == 102 then
hugg()
elseif k == "z" and attack == false and ModeOfGlitch == 3 then
CorruptionEvent()
elseif k == "z" and attack == false and ModeOfGlitch == 4 then
RapidBurst()
elseif k == "z" and attack == false and ModeOfGlitch == 765688533321 then
PowerBeams()
elseif k == "b" and attack == false and ModeOfGlitch == 765688533321 then
Unstable_Taunt()
elseif k == "z" and attack == false and ModeOfGlitch == 103 then
CardStorm()
elseif k == "z" and attack == false and ModeOfGlitch == 664663666 then
ViolentStrike()
elseif k == "z" and attack == false and ModeOfGlitch == 5 then
DivineSwarm()
elseif k == "z" and attack == false and ModeOfGlitch == 6 then
EquinoxOrbs()
elseif k == "z" and attack == false and ModeOfGlitch == 1264532489 then
FallenOrbs()
elseif k == "x" and attack == false and ModeOfGlitch == 5 then
HolyBarrier()
elseif k == "x" and attack == false and ModeOfGlitch == 8889 then
ExtCalbeam()
elseif k == "x" and attack == false and ModeOfGlitch == 88893333388 then
ExtCatbeam()
elseif k == "z" and attack == false and ModeOfGlitch == 090 then
RainbowBeam()
elseif k == "z" and attack == false and ModeOfGlitch == 1236 then
shytaunty()
elseif k == "x" and attack == false and ModeOfGlitch == 1236 then
lovesqueal()
elseif k == "v" and attack == false and ModeOfGlitch == 999999999556 then
CrazedInsanity()
elseif k == "z" and attack == false and ModeOfGlitch == 4367677813 then
SHDTwist()
end
if k == "v" and attack == false and ModeOfGlitch == 1264532489 then
FallenDEMISE()
end
if k == "x" and attack == false and ModeOfGlitch == 1 then
EndGROUND()
end
end)

mouse.KeyUp:connect(function(k)
	if k == "x" and ModeOfGlitch == 5 then
	shielding = false
	end
end)

coroutine.resume(coroutine.create(function()
while true do
swait()
if ModeOfGlitch ~= 666666 and ModeOfGlitch ~= 102 and ModeOfGlitch ~= 2332 and ModeOfGlitch ~= 1 and ModeOfGlitch ~= 2334 then
tr1.Enabled = true
tr2.Enabled = true
tr3.Enabled = true
tl1.Enabled = true
tl2.Enabled = true
tl3.Enabled = true
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(mw2:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
elseif ModeOfGlitch == 1 or ModeOfGlitch == 102 or ModeOfGlitch == 2332 or ModeOfGlitch == 2334 then
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(mw2:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Material = "Neon"
end
end
tr1.Enabled = true
tr2.Enabled = true
tr3.Enabled = true
tl1.Enabled = false
tl2.Enabled = false
tl3.Enabled = false
elseif ModeOfGlitch == 666666 then
tr1.Enabled = false
tr2.Enabled = false
tr3.Enabled = false
tl1.Enabled = false
tl2.Enabled = false
tl3.Enabled = false
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
v.Material = "Glass"
end
end
for i, v in pairs(mw2:GetChildren()) do
if v:IsA("Part") then
v.Material = "Glass"
end
end
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.Material = "Glass"
end
end
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.Material = "Glass"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Material = "Glass"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Material = "Glass"
end
end
end
end
end))


--[[coroutine.resume(coroutine.create(function()
while true do
swait(2)
if chaosmode == true then
tl1.Color = ColorSequence.new(BrickColor.random().Color)
tl2.Color = ColorSequence.new(BrickColor.random().Color)
tl3.Color = ColorSequence.new(BrickColor.random().Color)
RecolorTextAndRename("CHAOS",Color3.new(0,0,0),BrickColor.random().Color,"Fantasy")
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.random()
v.Material = "Neon"
end
end
for i, v in pairs(m2:GetChildren()) do
if v:IsA("Part") then
v.BrickColor = BrickColor.random()
v.Material = "Neon"
end
end
end
end
end))]]--
Humanoid.MaxHealth = math.huge
Humanoid.Health = math.huge

local bguis = Instance.new("BillboardGui",tors)
bguis.Size = UDim2.new(25, 0, 25, 0)
local bguis2 = Instance.new("BillboardGui",tors)
bguis2.Size = UDim2.new(25, 0, 25, 0)
local bguis3 = Instance.new("BillboardGui",tors)
bguis3.Size = UDim2.new(25, 0, 25, 0)
local bguis4 = Instance.new("BillboardGui",tors)
bguis4.Size = UDim2.new(25, 0, 25, 0)
local imgca = Instance.new("ImageLabel",bguis)
imgca.BackgroundTransparency = 1
imgca.ImageTransparency = 1
imgca.Size = UDim2.new(1,0,1,0)
imgca.Image = "rbxassetid://2344830904" --997291547,521073910,2312119891,2344830904
imgca.ImageColor3 = Color3.new(0,0,0)
local imgca2 = Instance.new("ImageLabel",bguis2)
imgca2.BackgroundTransparency = 1
imgca2.ImageTransparency = 1
imgca2.Size = UDim2.new(1,0,1,0)
imgca2.Image = "rbxassetid://2312119891" --997291547,521073910,2312119891,2344830904
imgca2.ImageColor3 = Color3.new(0,0,0)
local imgca3 = Instance.new("ImageLabel",bguis3)
imgca3.BackgroundTransparency = 1
imgca3.ImageTransparency = 1
imgca3.Size = UDim2.new(1,0,1,0)
imgca3.Image = "rbxassetid://2076519836" --997291547,521073910,2312119891,2344830904
imgca3.ImageColor3 = Color3.new(0,0,0)
local imgca4 = Instance.new("ImageLabel",bguis4)
imgca4.BackgroundTransparency = 1
imgca4.ImageTransparency = 1
imgca4.Size = UDim2.new(1,0,1,0)
imgca4.Image = "rbxassetid://2076458450" --997291547,521073910,2312119891,2344830904
imgca4.ImageColor3 = Color3.new(0,0,0)

idleanim=.4
while true do
if mutedtog == false then
kan.Volume = currentVol
elseif mutedtog == true then
kan.Volume = 0
end
pcall(function()
kan.PlaybackSpeed = currentPitch
kan.Pitch = currentPitch
kan.SoundId = currentThemePlaying
kan.Looped = true
kan.Parent = char
kan:Resume()
end)
coroutine.resume(coroutine.create(function()
if Error == true then
MAINRUINCOLOR = BrickColor.random()
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.random()
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.random()
v.Material = "Neon"
end
end
tl4.Color = ColorSequence.new(BrickColor.random().Color)
tl5.Color = ColorSequence.new(BrickColor.random().Color)
tl6.Color = ColorSequence.new(BrickColor.random().Color)
tr4.Color = ColorSequence.new(BrickColor.random().Color)
tr5.Color = ColorSequence.new(BrickColor.random().Color)
tr6.Color = ColorSequence.new(BrickColor.random().Color)
refec.Color = ColorSequence.new(BrickColor.Random().Color)
RecolorTextAndRename(est[math.random(1,17)],Color3.new(0,0,0),Color3.new(MRANDOM(0,1),MRANDOM(0,1),MRANDOM(0,1)),"Bodoni")
end
end))
coroutine.resume(coroutine.create(function()
	if CRAZED == true then
MAINRUINCOLOR = BrickColor.new("Navy blue")
refec.Color = ColorSequence.new(BrickColor.new("Really blue").Color)
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Navy blue")
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Really black")
v.Material = "Neon"
end
end
RecolorTextAndRename("CRAZED",Color3.new(0,0,math.random(0,0.6)),Color3.new(0,0,math.random(0.2,1)),"Code")
if math.random(1,2) == 1 then
MAINRUINCOLOR = BrickColor.new("Really black")
refec.Color = ColorSequence.new(BrickColor.new("Really black").Color)
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 0
v.BrickColor = BrickColor.new("Navy blue")
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Really black")
v.Material = "Neon"
end
end
end
	end
end))

coroutine.resume(coroutine.create(function()
if Error == true then
MAINRUINCOLOR = BrickColor.random()
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 0
v.BrickColor = BrickColor.random()
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.random()
v.Material = "Neon"
end
end
tl4.Color = ColorSequence.new(BrickColor.random().Color)
tl5.Color = ColorSequence.new(BrickColor.random().Color)
tl6.Color = ColorSequence.new(BrickColor.random().Color)
tr4.Color = ColorSequence.new(BrickColor.random().Color)
tr5.Color = ColorSequence.new(BrickColor.random().Color)
tr6.Color = ColorSequence.new(BrickColor.random().Color)
refec.Color = ColorSequence.new(BrickColor.Random().Color)
RecolorTextAndRename(est[math.random(1,17)],Color3.new(0,0,0),Color3.new(MRANDOM(0,1),MRANDOM(0,1),MRANDOM(0,1)),"Bodoni")
end
end))
coroutine.resume(coroutine.create(function()
	if unstablemode == true then
MAINRUINCOLOR = BrickColor.new("Really black")
refec.Color = ColorSequence.new(BrickColor.new("Really red").Color)
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Really red")
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Really black")
v.Material = "Neon"
end
end
RecolorTextAndRename("UNSTABLE",Color3.new(1,1,1),Color3.new(math.random(0.3,1),0,0),"Code")
if math.random(1,8) == 1 then
MAINRUINCOLOR = BrickColor.new(math.random(0.2,1),0,0)
end
if math.random(1,10) == 1 then
RecolorTextAndRename("UNSTABLE",Color3.new(math.random(0.3,1),0,0),Color3.new(0,0,0),"Code")
refec.Color = ColorSequence.new(BrickColor.new("Really black").Color)
for i, v in pairs(extrawingmod2:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Really red")
v.Material = "Neon"
end
end
for i, v in pairs(extrawingmod1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.new("Really black")
v.Material = "Neon"
end
end
end
end
end))

coroutine.resume(coroutine.create(function()
	if chaosmode == true then
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
v.Transparency = 1
v.BrickColor = BrickColor.random()
v.Material = "Neon"
end
end
tl1.Color = ColorSequence.new(BrickColor.random().Color)
tl2.Color = ColorSequence.new(BrickColor.random().Color)
tl3.Color = ColorSequence.new(BrickColor.random().Color)
	RecolorTextAndRename("CHAOS",Color3.new(0,0,0),BrickColor.random().Color,"Arcade")
	end
end))

if chaosmode == false and CRAZED == false and Error == false and unstablemode == false and ModeOfGlitch ~= 666666 then
modet.Position = UDim2.new(0,0,0,0)
modet.Rotation = -5 * math.cos(sine / 32)
techc.Rotation = techc.Rotation + 1
circl.Rotation = circl.Rotation - kan.PlaybackLoudness/100 - 1
circl2.Rotation = circl2.Rotation + kan.PlaybackLoudness/75 + 1
imgl2.Rotation = imgl2.Rotation - kan.PlaybackLoudness/75 + 1
imgl2b.Rotation = imgl2b.Rotation + kan.PlaybackLoudness/50 - 1
wobble.Rotation = 0 - 1 * math.cos(sine / 24)
wobble.BackgroundColor3 = modet.TextColor3
wobble.BorderColor3 = modet.TextStrokeColor3
wobble.BorderSizePixel = 2
wobble2.Rotation = 0 - 1 * math.cos(sine / 30)
wobble2.BackgroundColor3 = modet.TextStrokeColor3
wobble2.BorderColor3 = modet.TextColor3
wobble2.BorderSizePixel = 2
TextFrame.TextColor3 = modet.TextColor3
TextFrame.TextStrokeColor3 = modet.TextStrokeColor3
TextFrame.TextStrokeTransparency = 0
TextFrame.Rotation = 0 - 2 * math.cos(sine / 30)
TextFrame.Position = UDim2.new(0.48,0 - 10 * math.cos(sine / 50),0.867,0 - 10 * math.cos(sine / 50))
DamageFrame.Rotation = 6 - 2 * math.cos(sine / 35)
DamageFrame.TextColor3 = modet.TextColor3
DamageFrame.TextStrokeColor3 = modet.TextStrokeColor3
DamageFrame.TextStrokeTransparency = 0
ModeFrame.Rotation = 6 - 2 * math.cos(sine / 35)
ModeFrame.TextColor3 = modet.TextColor3
ModeFrame.TextStrokeColor3 = modet.TextStrokeColor3
ModeFrame.TextStrokeTransparency = 0
Visuals.Rotation = Visuals.Rotation + 1
Visuals.BackgroundColor3 = modet.TextStrokeColor3
Visuals.BorderColor3 = modet.TextColor3
Visuals.BorderSizePixel = 4
glow.ImageColor3 = modet.TextStrokeColor3
Visuals2.Rotation = Visuals2.Rotation - 1
Visuals2.BackgroundColor3 = modet.TextStrokeColor3
Visuals2.BorderColor3 = modet.TextColor3
Visuals2.BorderSizePixel = 4
ned.Rotation = 0 - 2 * math.cos(sine / 24)
ned.Position = UDim2.new(0.7,0 - 10 * math.cos(sine / 32),0.8,0 - 10 * math.cos(sine / 45))
elseif ModeOfGlitch == 666666 then
modet.TextColor3 = Color3.new(0,0,0)
modet.TextStrokeColor3 = BrickColor.new("Crimson").Color
	techc.Rotation = techc.Rotation + 3
circl.Rotation = circl.Rotation + 1 - kan.PlaybackLoudness/25
circl2.Rotation = circl2.Rotation + 1 + kan.PlaybackLoudness/50
imgl2.Rotation = imgl2.Rotation + 1 - kan.PlaybackLoudness/50
imgl2b.Rotation = imgl2b.Rotation + 1 + kan.PlaybackLoudness/25
TextFrame.TextColor3 = modet.TextColor3
TextFrame.TextStrokeColor3 = modet.TextStrokeColor3
TextFrame.TextStrokeTransparency = 0
TextFrame.Rotation = 0 + math.random(-3,3)
TextFrame.Position = UDim2.new(0.48,0 + math.random(-1.5,1.5),0.867,0 + math.random(-1.5,1.5)) -- backori
DamageFrame.Rotation = 6 + math.random(-2,2)
DamageFrame.TextColor3 = modet.TextColor3
DamageFrame.TextStrokeColor3 = modet.TextStrokeColor3
DamageFrame.TextStrokeTransparency = 0
ModeFrame.Rotation = 6 + math.random(-2,2)
ModeFrame.TextColor3 = modet.TextColor3
ModeFrame.TextStrokeColor3 = modet.TextStrokeColor3
ModeFrame.TextStrokeTransparency = 0
glow.ImageColor3 = modet.TextStrokeColor3
wobble.Rotation = 0 - 2 * math.cos(sine / 24)
wobble2.Rotation = 0 - 2 * math.cos(sine / 30)
wobble2.BackgroundColor3 = modet.TextStrokeColor3
wobble2.BorderColor3 = modet.TextColor3
wobble2.BorderSizePixel = 2
Visuals.Rotation = Visuals.Rotation + 2 + math.random(-2.5,2.5)
Visuals2.Rotation = Visuals2.Rotation + 2 + math.random(-5,5)
Visuals.BackgroundColor3 = modet.TextStrokeColor3
Visuals.BorderColor3 = modet.TextColor3
Visuals2.BackgroundColor3 = modet.TextStrokeColor3
Visuals2.BorderColor3 = modet.TextColor3
wobble.BackgroundColor3 = modet.TextColor3
wobble.BorderColor3 = modet.TextStrokeColor3
wobble.BorderSizePixel = 2
ned.Rotation = 0 -2 * math.cos(sine / 1) + math.random(-1.5,1.5)
ned.Position = UDim2.new(0.7,0 + math.random(-1.5,1.5),0.8,0 + math.random(-1.5,1.5))
modet.Position = UDim2.new(0,math.random(-1,1),0,math.random(-1,1))
modet.Rotation = -2 * math.cos(sine / 1) + math.random(-1.5,1.5)
elseif unstablemode == true then
modet.Position = UDim2.new(0,0,0,0)
modet.Rotation = -5 * math.cos(sine / 12)
techc.Rotation = techc.Rotation + 2
circl.Rotation = circl.Rotation - kan.PlaybackLoudness/75 - 2
circl2.Rotation = circl2.Rotation + kan.PlaybackLoudness/50 + 2
imgl2.Rotation = imgl2.Rotation - kan.PlaybackLoudness/50 + 2
imgl2b.Rotation = imgl2b.Rotation + kan.PlaybackLoudness/25 - 2
wobble.Rotation = 0 - 2 * math.cos(sine / 24)
wobble.BackgroundColor3 = modet.TextColor3
wobble.BorderColor3 = modet.TextStrokeColor3
wobble.BorderSizePixel = 2
wobble2.Rotation = 0 - 2 * math.cos(sine / 30)
wobble2.BackgroundColor3 = modet.TextStrokeColor3
wobble2.BorderColor3 = modet.TextColor3
wobble2.BorderSizePixel = 2
TextFrame.TextColor3 = modet.TextColor3
TextFrame.TextStrokeColor3 = modet.TextStrokeColor3
TextFrame.TextStrokeTransparency = 0
TextFrame.Rotation = 0 - 4 * math.cos(sine / 30)
TextFrame.Position = UDim2.new(0.48,0 - 10 * math.cos(sine / 50),0.867,0 - 10 * math.cos(sine / 50))
DamageFrame.Rotation = 6 - 4 * math.cos(sine / 35)
DamageFrame.TextColor3 = modet.TextColor3
DamageFrame.TextStrokeColor3 = modet.TextStrokeColor3
DamageFrame.TextStrokeTransparency = 0
ModeFrame.Rotation = 6 - 4 * math.cos(sine / 35)
ModeFrame.TextColor3 = modet.TextColor3
ModeFrame.TextStrokeColor3 = modet.TextStrokeColor3
ModeFrame.TextStrokeTransparency = 0
Visuals.Rotation = Visuals.Rotation + 2
Visuals.BackgroundColor3 = modet.TextStrokeColor3
Visuals.BorderColor3 = modet.TextColor3
Visuals.BorderSizePixel = 4
glow.ImageColor3 = modet.TextStrokeColor3
Visuals2.Rotation = Visuals2.Rotation - 2
Visuals2.BackgroundColor3 = modet.TextStrokeColor3
Visuals2.BorderColor3 = modet.TextColor3
Visuals2.BorderSizePixel = 4
ned.Rotation = 0 - 6 * math.cos(sine / 24)
ned.Position = UDim2.new(0.7,0 - 10 * math.cos(sine / 16),0.8,0 - 10 * math.cos(sine / 45))	
elseif chaosmode == true then
	techc.Rotation = techc.Rotation + 1
circl.Rotation = circl.Rotation - kan.PlaybackLoudness/50 + math.random(-5,5)
circl2.Rotation = circl2.Rotation + kan.PlaybackLoudness/25 + math.random(-5,5)
imgl2.Rotation = imgl2.Rotation - kan.PlaybackLoudness/25 + math.random(-5,5)
imgl2b.Rotation = imgl2b.Rotation + kan.PlaybackLoudness/12.5 + math.random(-5,5)
TextFrame.TextColor3 = modet.TextColor3
TextFrame.TextStrokeColor3 = modet.TextStrokeColor3
TextFrame.TextStrokeTransparency = 0
TextFrame.Rotation = 0 + math.random(-3,3)
TextFrame.Position = UDim2.new(0.48,0 + math.random(-3,3),0.867,0 + math.random(-3,3)) -- backori
DamageFrame.Rotation = 6 + math.random(-2,2)
DamageFrame.TextColor3 = modet.TextColor3
DamageFrame.TextStrokeColor3 = modet.TextStrokeColor3
DamageFrame.TextStrokeTransparency = 0
ModeFrame.Rotation = 6 + math.random(-2,2)
ModeFrame.TextColor3 = modet.TextColor3
ModeFrame.TextStrokeColor3 = modet.TextStrokeColor3
ModeFrame.TextStrokeTransparency = 0
glow.ImageColor3 = modet.TextStrokeColor3
wobble.Rotation = 0 - 2 * math.cos(sine / 24)
wobble2.Rotation = 0 - 2 * math.cos(sine / 30)
wobble2.BackgroundColor3 = modet.TextStrokeColor3
wobble2.BorderColor3 = modet.TextColor3
wobble2.BorderSizePixel = 2
Visuals.Rotation = Visuals.Rotation + math.random(-5,5)
Visuals2.Rotation = Visuals2.Rotation + math.random(-10,10)
Visuals.BackgroundColor3 = modet.TextStrokeColor3
Visuals.BorderColor3 = modet.TextColor3
Visuals2.BackgroundColor3 = modet.TextStrokeColor3
Visuals2.BorderColor3 = modet.TextColor3
wobble.BackgroundColor3 = modet.TextColor3
wobble.BorderColor3 = modet.TextStrokeColor3
wobble.BorderSizePixel = 2
ned.Rotation = 0 -2 * math.cos(sine / 1) + math.random(-3,3)
ned.Position = UDim2.new(0.7,0 + math.random(-3,3),0.8,0 + math.random(-3,3))
modet.Position = UDim2.new(0,math.random(-1,1),0,math.random(-1,1))
modet.Rotation = -2 * math.cos(sine / 1) + math.random(-3,3)
elseif Error == true then
	techc.Rotation = techc.Rotation + 15
circl.Rotation = circl.Rotation - 15 - kan.PlaybackLoudness/50 + math.random(-11,11)
circl2.Rotation = circl2.Rotation + 15 + kan.PlaybackLoudness/25 + math.random(-11,11)
imgl2.Rotation = imgl2.Rotation - 15 - kan.PlaybackLoudness/25 + math.random(-11,11)
imgl2b.Rotation = imgl2b.Rotation + 15 + kan.PlaybackLoudness/12.5 + math.random(-11,11)
TextFrame.TextColor3 = modet.TextColor3
TextFrame.TextStrokeColor3 = modet.TextStrokeColor3
TextFrame.TextStrokeTransparency = 0
TextFrame.Rotation = 0 + math.random(-11,11)
TextFrame.Position = UDim2.new(0.48,0 + math.random(-9,9),0.867,0 + math.random(-9,9)) -- backori
DamageFrame.Rotation = 18 + math.random(-9,9)
DamageFrame.TextColor3 = modet.TextColor3
DamageFrame.TextStrokeColor3 = modet.TextStrokeColor3
DamageFrame.TextStrokeTransparency = 0
ModeFrame.Rotation = 18 + math.random(-9,9)
ModeFrame.TextColor3 = modet.TextColor3
ModeFrame.TextStrokeColor3 = modet.TextStrokeColor3
ModeFrame.TextStrokeTransparency = 0
glow.ImageColor3 = modet.TextStrokeColor3
wobble.Rotation = 0 - 7.5 * math.cos(sine / 24)
wobble2.Rotation = 0 - 8 * math.cos(sine / 30)
wobble2.BackgroundColor3 = modet.TextStrokeColor3
wobble2.BorderColor3 = modet.TextColor3
wobble2.BorderSizePixel = 2
Visuals.Rotation = math.random(-35,35)
Visuals2.Rotation = math.random(-45,45)
Visuals.BackgroundColor3 = modet.TextStrokeColor3
Visuals.BorderColor3 = modet.TextColor3
Visuals2.BackgroundColor3 = modet.TextStrokeColor3
Visuals2.BorderColor3 = modet.TextColor3
wobble.BackgroundColor3 = modet.TextColor3
wobble.BorderColor3 = modet.TextStrokeColor3
wobble.BorderSizePixel = 2
ned.Rotation = 0 -6 * math.cos(sine / 1) + math.random(-9,9)
ned.Position = UDim2.new(0.7,0 + math.random(-3,3),0.8,0 + math.random(-9,9))
modet.Position = UDim2.new(0,math.random(-1,1),0,math.random(-4,4))
modet.Rotation = -6 * math.cos(sine / 1) + math.random(-9,9)
elseif CRAZED == true then
	techc.Rotation = techc.Rotation + 3
circl.Rotation = circl.Rotation - 5 - kan.PlaybackLoudness/125 + math.random(-2,2)
circl2.Rotation = circl2.Rotation + 5 + kan.PlaybackLoudness/100 + math.random(-2,2)
imgl2.Rotation = imgl2.Rotation - 5 - kan.PlaybackLoudness/100 + math.random(-2,2)
imgl2b.Rotation = imgl2b.Rotation + 5 + kan.PlaybackLoudness/75 + math.random(-2,2)
TextFrame.TextColor3 = modet.TextColor3
TextFrame.TextStrokeColor3 = modet.TextStrokeColor3
TextFrame.TextStrokeTransparency = 0
TextFrame.Rotation = 0 + math.random(-4,4)
TextFrame.Position = UDim2.new(0.48,0 + math.random(-4,4),0.867,0 + math.random(-4,4)) -- backori
DamageFrame.Rotation = 6 + math.random(-4,4)
DamageFrame.TextColor3 = modet.TextColor3
DamageFrame.TextStrokeColor3 = modet.TextStrokeColor3
DamageFrame.TextStrokeTransparency = 0
ModeFrame.Rotation = 6 + math.random(-4,4)
ModeFrame.TextColor3 = modet.TextColor3
ModeFrame.TextStrokeColor3 = modet.TextStrokeColor3
ModeFrame.TextStrokeTransparency = 0
glow.ImageColor3 = modet.TextStrokeColor3
wobble.Rotation = 0 - 4 * math.cos(sine / 24)
wobble2.Rotation = 0 - 4 * math.cos(sine / 30)
wobble2.BackgroundColor3 = modet.TextStrokeColor3
wobble2.BorderColor3 = modet.TextColor3
wobble2.BorderSizePixel = 2
Visuals.Rotation = Visuals.Rotation + math.random(-7,7)
Visuals2.Rotation = Visuals2.Rotation + math.random(-12,12)
Visuals.BackgroundColor3 = modet.TextStrokeColor3
Visuals.BorderColor3 = modet.TextColor3
Visuals2.BackgroundColor3 = modet.TextStrokeColor3
Visuals2.BorderColor3 = modet.TextColor3
wobble.BackgroundColor3 = modet.TextColor3
wobble.BorderColor3 = modet.TextStrokeColor3
wobble.BorderSizePixel = 2
ned.Rotation = 0 -4 * math.cos(sine / 1) + math.random(-4,4)
ned.Position = UDim2.new(0.7,0 + math.random(-3,3),0.8,0 + math.random(-4,4))
modet.Position = UDim2.new(0,math.random(-2,2),0,math.random(-2,2))
modet.Rotation = -4 * math.cos(sine / 1) + math.random(-4,4)
end
if rainbowmode == true then
RecolorTextAndRename("RAINBOW",Color3.new(r/255,g/255,b/255),Color3.new(1,1,1),"Highway")
if ModeOfGlitch == 909090 then
RecolorTextAndRename("AESTHETIC",Color3.new(r/255,g/255,b/255),Color3.new(r/255,g/255,b/255),"Fantasy")
end
MAINRUINCOLOR = BrickColor.new(r/255,g/255,b/255)
modet.TextColor3 = Color3.new(r/255,g/255,b/255)
refec.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
tr1.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
tr2.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
tr3.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
tl1.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
tl2.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
tl3.Color = ColorSequence.new(Color3.new(r/255,g/255,b/255))
for i, v in pairs(m:GetChildren()) do
if v:IsA("Part") then
v.Color = Color3.new(r/255,g/255,b/255)
end
end
for i, v in pairs(m2:GetChildren()) do
if v:IsA("Part") then
v.Color = Color3.new(r/255,g/255,b/255)
end
end
for i, v in pairs(m3:GetChildren()) do
if v:IsA("Part") then
v.Color = Color3.new(r/255,g/255,b/255)
end
end
for i, v in pairs(mw1:GetChildren()) do
if v:IsA("Part") then
v.Color = Color3.new(r/255,g/255,b/255)
v.Material = "Neon"
end
end
for i, v in pairs(mw2:GetChildren()) do
if v:IsA("Part") then
v.Color = Color3.new(r/255,g/255,b/255)
v.Material = "Neon"
end
end
end

CameraManager()

swait()
if ModeOfGlitch ~= 1264532489 and ModeOfGlitch ~= 666666 and ModeOfGlitch ~= 343434 and ModeOfGlitch ~= 343435 and ModeOfGlitch ~= 909090 and ModeOfGlitch ~= 1236 and ModeOfGlitch ~= 146536 and ModeOfGlitch ~= 6518594185 and ModeOfGlitch ~= 71 and ModeOfGlitch ~= 676767 and ModeOfGlitch ~= 102341 and ModeOfGlitch ~= 61 and ModeOfGlitch ~= 999 and ModeOfGlitch ~= 3434 and ModeOfGlitch ~= 453453484635345 and ModeOfGlitch ~= 3444 and ModeOfGlitch ~= 103 and ModeOfGlitch ~= 102 and ModeOfGlitch ~= 101 and ModeOfGlitch ~= 55469696922 and ModeOfGlitch ~= 4367677813 and ModeOfGlitch ~= 9999999921111 and ModeOfGlitch ~= 999999999556 and ModeOfGlitch ~= 765688533321 and ModeOfGlitch ~= 8889 and ModeOfGlitch ~= 664663666 and ModeOfGlitch ~= 88893333388 and ModeOfGlitch ~= 808080808080808080808080 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(2,0,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(5 + 10 * math.cos(sine / 32)),math.rad(0),math.rad(12.5 + 5 * math.cos(sine / 32))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(3,1,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(10 + 15 * math.cos(sine / 32)),math.rad(0),math.rad(25 + 7.5 * math.cos(sine / 32))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(3.75,2,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(15 + 20 * math.cos(sine / 32)),math.rad(0),math.rad(37.5 + 10 * math.cos(sine / 32))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(-2,0,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(5 + 10 * math.cos(sine / 32)),math.rad(0),math.rad(-12.5 - 5 * math.cos(sine / 32))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(-3,1,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(10 + 15 * math.cos(sine / 32)),math.rad(0),math.rad(-25 - 7.5 * math.cos(sine / 32))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(-3.75,2,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(15 + 20 * math.cos(sine / 32)),math.rad(0),math.rad(-37.5 - 10 * math.cos(sine / 32))),.3)
elseif ModeOfGlitch == 676767 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(1 + 5 * math.cos(sine / 180),4 + 2.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(1 + 5 * math.cos(sine / 180),4 + 2.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(1 + 5 * math.cos(sine / 180),4 + 2.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(1 + 5 * math.cos(sine / 180),4 + 2.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 + 3600 * math.cos(sine / 360))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(1 + 5 * math.cos(sine / 180),4 + 2.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 + 3600 * math.cos(sine / 360))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(1 + 5 * math.cos(sine / 180),4 + 2.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 + 3600 * math.cos(sine / 360))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 3600 * math.cos(sine / 360))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 3600 * math.cos(sine / 360))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 3600 * math.cos(sine / 360))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 3600 * math.cos(sine / 360))),.3)
elseif ModeOfGlitch == 55469696922 or ModeOfGlitch == 4367677813 or ModeOfGlitch == 9999999921111 or ModeOfGlitch == 999 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 + 3600 * math.cos(sine / 360))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 + 3600 * math.cos(sine / 360))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 + 3600 * math.cos(sine / 360))),.3)
elseif ModeOfGlitch == 3434 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(2,0,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(5 + 10 * math.cos(sine / 32)),math.rad(0),math.rad(12.5 + 5 * math.cos(sine / 32))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(3,1,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(10 + 15 * math.cos(sine / 32)),math.rad(0),math.rad(25 + 7.5 * math.cos(sine / 32))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(3.75,2,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(15 + 20 * math.cos(sine / 32)),math.rad(0),math.rad(37.5 + 10 * math.cos(sine / 32))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(-2,0,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(5 + 10 * math.cos(sine / 32)),math.rad(0),math.rad(-12.5 - 5 * math.cos(sine / 32))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(-3,1,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(10 + 15 * math.cos(sine / 32)),math.rad(0),math.rad(-25 - 7.5 * math.cos(sine / 32))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(-3.75,2,0)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(15 + 20 * math.cos(sine / 32)),math.rad(0),math.rad(-37.5 - 10 * math.cos(sine / 32))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 3600 * math.cos(sine / 360))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 3600 * math.cos(sine / 360))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 3600 * math.cos(sine / 360))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 3600 * math.cos(sine / 360))),.3)
elseif ModeOfGlitch == 453453484635345 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 720))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 720))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 720))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 3600 * math.cos(sine / 360))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 3600 * math.cos(sine / 360))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 3600 * math.cos(sine / 360))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 3600 * math.cos(sine / 360))),.3)
elseif ModeOfGlitch == 8889 or ModeOfGlitch == 1236 or ModeOfGlitch == 343434 or ModeOfGlitch == 71 or ModeOfGlitch == 664663666 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 720))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 720))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 720))),.3)
elseif ModeOfGlitch == 343435 or ModeOfGlitch == 88893333388 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 720))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 720))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 720))),.3)

lwing4weld.C1=clerp(lwing4weld.C1,cf(2.5 + 5 * math.cos(sine / 180),0.5 + 1.5 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(2.5 + 5 * math.cos(sine / 180),0.5 + 1.5 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(2.5 + 5 * math.cos(sine / 180),0.5 + 1.5 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 360))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(1.5 + 3 * math.cos(sine / 360),-0.25 - 1 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 720))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(1.5 + 3 * math.cos(sine / 360),-0.25 - 1 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 720))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(1.5 + 3 * math.cos(sine / 360),-0.25 - 1 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 720))),.3)
elseif ModeOfGlitch == 666666 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 320),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 + 5400 * math.cos(sine / 1008))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 320),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 + 5400 * math.cos(sine / 1008))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 320),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 + 5400 * math.cos(sine / 1008))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 320),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 + 5400 * math.cos(sine / 1008))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 320),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 + 5400 * math.cos(sine / 1008))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 320),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 + 5400 * math.cos(sine / 1008))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(2.5 + 5 * math.cos(sine / 180),0.4 + 1.5 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 540))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(2.5 + 5 * math.cos(sine / 180),0.4 + 1.5 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 540))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(2.5 + 5 * math.cos(sine / 180),0.4 + 1.5 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 540))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(1.5 + 3 * math.cos(sine / 504),-0.3 - 1.1 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 5400 * math.cos(sine / 1008))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(1.5 + 3 * math.cos(sine / 504),-0.3 - 1.1 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 5400 * math.cos(sine / 1008))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(1.5 + 3 * math.cos(sine / 504),-0.3 - 1.1 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 5400 * math.cos(sine / 1008))),.3)

elseif ModeOfGlitch == 3444 then
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0.25)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50) - math.random(-60,60) * math.cos(sine / 1)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 + 3600 * math.cos(sine / 360) - 90 * math.cos(sine / 1))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70) - math.random(-60,60) * math.cos(sine / 1)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 + 3600 * math.cos(sine / 360) - 90 * math.cos(sine / 1))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60) - math.random(-60,60) * math.cos(sine / 1)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 + 3600 * math.cos(sine / 360) - 90 * math.cos(sine / 1))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50) - math.random(-60,60) * math.cos(sine / 1)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 + 3600 * math.cos(sine / 360) - 90 * math.cos(sine / 1))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70) - math.random(-60,60) * math.cos(sine / 1)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 + 3600 * math.cos(sine / 360) - 90 * math.cos(sine / 1))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60) - math.random(-60,60) * math.cos(sine / 1)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 + 3600 * math.cos(sine / 360) - 90 * math.cos(sine / 1))),.3)
elseif ModeOfGlitch == 102 then
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handlexweld.C0=clerp(handlexweld.C0,cf(0 + 0.25 * math.cos(sine / 63),0 + 0.25 * math.cos(sine / 70),0 + 0.05 * math.cos(sine / 57))*angles(math.rad(0 + 2 * math.cos(sine / 55)),math.rad(0 + 2 * math.cos(sine / 46)),math.rad(0 + 2 * math.cos(sine / 32))),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0,1.85 + 0.15 * math.cos(sine / 36),0)*angles(math.rad(0 + 3 * math.cos(sine / 42)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 + 5 * math.cos(sine / 56))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0,1.85 + 0.15 * math.cos(sine / 38),0)*angles(math.rad(0 + 3 * math.cos(sine / 45)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(130 + 5 * math.cos(sine / 56))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0,1.85 + 0.15 * math.cos(sine / 41),0)*angles(math.rad(0 + 3 * math.cos(sine / 48)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(50 + 5 * math.cos(sine / 56))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0,1.85 + 0.15 * math.cos(sine / 36),0)*angles(math.rad(0 + 3 * math.cos(sine / 46)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 5 * math.cos(sine / 56))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0,1.85 + 0.15 * math.cos(sine / 38),0)*angles(math.rad(0 + 3 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-130 - 5 * math.cos(sine / 56))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0,1.85 + 0.15 * math.cos(sine / 41),0)*angles(math.rad(0 + 3 * math.cos(sine / 40)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-50 - 5 * math.cos(sine / 56))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 360))),.3)
elseif ModeOfGlitch == 103 then
handleweld.C0=clerp(handleweld.C0,cf(0,-1.5,-1.5)*angles(math.rad(90),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 720))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 720))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),1.5)*angles(math.rad(90 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 720))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 3600 * math.cos(sine / 360))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 3600 * math.cos(sine / 360))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 3600 * math.cos(sine / 360))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 3600 * math.cos(sine / 360))),.3)
elseif ModeOfGlitch == 102341 then
lwing1weld.C1=clerp(lwing1weld.C1,cf(2,0,-0.4)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(5 + 10 * math.cos(sine / 32)),math.rad(0),math.rad(12.5 + 5 * math.cos(sine / 32))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(3,1,-0.4)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(10 + 15 * math.cos(sine / 32)),math.rad(0),math.rad(25 + 7.5 * math.cos(sine / 32))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(3.75,2,-0.4)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(15 + 20 * math.cos(sine / 32)),math.rad(0),math.rad(37.5 + 10 * math.cos(sine / 32))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(-2,0,-0.4)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(5 + 10 * math.cos(sine / 32)),math.rad(0),math.rad(-12.5 - 5 * math.cos(sine / 32))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(-3,1,-0.4)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(10 + 15 * math.cos(sine / 32)),math.rad(0),math.rad(-25 - 7.5 * math.cos(sine / 32))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(-3.75,2,-0.3)*angles(math.rad(0),math.rad(0),math.rad(0))*angles(math.rad(15 + 20 * math.cos(sine / 32)),math.rad(0),math.rad(-37.5 - 10 * math.cos(sine / 32))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0,2 - 1.5 * math.cos(sine / 32.5),0)*angles(math.rad(0),math.rad(0),math.rad(360 + 720 * math.cos(sine / 65))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0,0 - 1 * math.cos(sine / 32.5),-0.8)*angles(math.rad(0 + 5 * math.cos(sine / 32.5)),math.rad(0),math.rad(360)),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0,2 - 1.5 * math.cos(sine / 32.5),0)*angles(math.rad(0),math.rad(0),math.rad(180 + 720 * math.cos(sine / 65))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0,2 - 1.5 * math.cos(sine / 32.5),0)*angles(math.rad(0),math.rad(0),math.rad(-90 + 720 * math.cos(sine / 65))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0,-1 + 1 * math.cos(sine / 32.5),-0.9)*angles(math.rad(0 + 5 * math.cos(sine / 32.5)),math.rad(0),math.rad(180)),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0,2 - 1.5 * math.cos(sine / 32.5),-0.9)*angles(math.rad(0),math.rad(0),math.rad(90 + 720 * math.cos(sine / 65))),.3)
elseif ModeOfGlitch == 101 then
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 720))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 720))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 1.5 * math.cos(sine / 360),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 720))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 3600 * math.cos(sine / 360))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 3600 * math.cos(sine / 360))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 3600 * math.cos(sine / 360))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 3600 * math.cos(sine / 360))),.3)
elseif ModeOfGlitch == 61 then
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 + 3600 * math.cos(sine / 160))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 + 3600 * math.cos(sine / 160))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),0.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 + 3600 * math.cos(sine / 160))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 1.5 * math.cos(sine / 230),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(0 - 3600 * math.cos(sine / 550))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 1.5 * math.cos(sine / 230),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(120 - 3600 * math.cos(sine / 550))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 1.5 * math.cos(sine / 230),-0.25 - 0.5 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-120 - 3600 * math.cos(sine / 550))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 3600 * math.cos(sine / 160))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 3600 * math.cos(sine / 160))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 3600 * math.cos(sine / 160))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 3600 * math.cos(sine / 160))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 3600 * math.cos(sine / 160))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 3600 * math.cos(sine / 160))),.3)
elseif ModeOfGlitch == 999999999556 then
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 + 4500 * math.cos(sine / 280))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 + 4500 * math.cos(sine / 280))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 + 4500 * math.cos(sine / 280))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 + 4500 * math.cos(sine / 280))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 + 4500 * math.cos(sine / 280))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 + 4500 * math.cos(sine / 280))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 4500 * math.cos(sine / 280))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 4500 * math.cos(sine / 280))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 4500 * math.cos(sine / 280))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 4500 * math.cos(sine / 280))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 4500 * math.cos(sine / 280))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 4500 * math.cos(sine / 280))),.3)
elseif ModeOfGlitch == 146536 then
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0,1.5 + 0.75 * math.cos(sine / 21),0)*angles(math.rad(0 + 1 + 1 * math.cos(sine / 1)),math.rad(0 - 1),1 + 1),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0,1.5 + 0.75 * math.cos(sine / 21),0)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 2),1 + 2),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0,1.5 + 0.75 * math.cos(sine / 21),0)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 3),1 + 3),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0,1.5 + 0.75 * math.cos(sine / 21),0)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 4),1 + 4),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0,1.5 + 0.75 * math.cos(sine / 21),0)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 5),1 + 5),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0,1.5 + 0.75 * math.cos(sine / 21),0)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 6),1 + 6),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 27),-1)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 7),1 + 7),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 28),-1)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 8),1 + 8),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 29),-1)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 9),1 + 9),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 30),-1)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 10),1 + 10),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 31),-1)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 11),1 + 11),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 32),-1)*angles(math.rad(0 + 1 * math.cos(sine / 1)),math.rad(0 - 12),1 + 12),.3)
elseif ModeOfGlitch == 808080808080808080808080  or ModeOfGlitch == 909090 or ModeOfGlitch == 765688533321 or ModeOfGlitch == 1264532489 or ModeOfGlitch == 6518594185 then
handlexweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
handleweld.C0=clerp(handleweld.C0,cf(0,0,0)*angles(math.rad(0),math.rad(0),math.rad(0)),.3)
lwing1weld.C1=clerp(lwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 + 3600 * math.cos(sine / 360))),.3)
lwing2weld.C1=clerp(lwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 + 3600 * math.cos(sine / 360))),.3)
lwing3weld.C1=clerp(lwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 + 3600 * math.cos(sine / 360))),.3)
rwing1weld.C1=clerp(rwing1weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 + 3600 * math.cos(sine / 360))),.3)
rwing2weld.C1=clerp(rwing2weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 + 3600 * math.cos(sine / 360))),.3)
rwing3weld.C1=clerp(rwing3weld.C1,cf(0 + 2.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),0)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 + 3600 * math.cos(sine / 360))),.3)
lwing4weld.C1=clerp(lwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(90 - 3600 * math.cos(sine / 360))),.3)
lwing5weld.C1=clerp(lwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(147.5 - 3600 * math.cos(sine / 360))),.3)
lwing6weld.C1=clerp(lwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(32.5 - 3600 * math.cos(sine / 360))),.3)
rwing4weld.C1=clerp(rwing4weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 50)),math.rad(0 - 2 * math.cos(sine / 36)),math.rad(-90 - 3600 * math.cos(sine / 360))),.3)
rwing5weld.C1=clerp(rwing5weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 70)),math.rad(0 - 2 * math.cos(sine / 37)),math.rad(-147.5 - 3600 * math.cos(sine / 360))),.3)
rwing6weld.C1=clerp(rwing6weld.C1,cf(0 - 7.5 * math.cos(sine / 180),1.5 + 0.75 * math.cos(sine / 25),-1)*angles(math.rad(0 + 1 * math.cos(sine / 60)),math.rad(0 - 2 * math.cos(sine / 51)),math.rad(-32.5 - 3600 * math.cos(sine / 360))),.3)
end
 sine = sine + change
local torvel=(RootPart.Velocity*Vector3.new(1,0,1)).magnitude 
local velderp=RootPart.Velocity.y
hitfloor,posfloor=rayCast(RootPart.Position,(CFrame.new(RootPart.Position,RootPart.Position - Vector3.new(0,1,0))).lookVector,4,Character)
coroutine.resume(coroutine.create(function()
if ModeOfGlitch == 666666 then
--slash(math.random(50,100)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-2.4,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(0.01,0.002,0.01),math.random(15,60)/250,BrickColor.new("Crimson"))
end
end))
coroutine.resume(coroutine.create(function()
if ModeOfGlitch == 71 then
	if hitfloor ~= nil then
	--sphere2(3,"Add",root.CFrame*CFrame.new(0,-3,0),vt(3,0.55,3),0.025,-0.01,0.025,MAINRUINCOLOR)
	end
end
end))
coroutine.resume(coroutine.create(function()
if ModeOfGlitch == 343434 then
	if hitfloor ~= nil then
--slash(math.random(50,100)/10,5,true,"Round","Add","Out",root.CFrame*CFrame.new(0,-3,0)*CFrame.Angles(math.rad(math.random(-5,5)),math.rad(math.random(-360,360)),math.rad(math.random(-5,5))),vt(0.01,0.01,0.01),math.random(5,50)/250,BrickColor.new("Neon orange"))
	end
end
end))
if equipped==true or equipped==false then
if attack==false then
idle=idle+1
else
idle=0
end
if idle>=500 then
if attack==false then
--Sheath()
end
end
if RootPart.Velocity.y > 1 and hitfloor==nil then 
Anim="Jump"
if attack==false then
RH.C0=clerp(RH.C0,cf(1,-0.35 - 0.05 * math.cos(sine / 25),-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-5),math.rad(0),math.rad(-20)),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 25),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-5),math.rad(0),math.rad(20)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 25))*angles(math.rad(-10),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-2.5),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(1.45,0.5 + 0.1 * math.cos(sine / 25),0)*angles(math.rad(-5),math.rad(0),math.rad(25)),.1)
LW.C0=clerp(LW.C0,cf(-1.45,0.5 + 0.1 * math.cos(sine / 25),0)*angles(math.rad(-5),math.rad(0),math.rad(-25)),.1)
end
elseif RootPart.Velocity.y < -1 and hitfloor==nil then 
Anim="Fall"
if attack==false then
RH.C0=clerp(RH.C0,cf(1,-0.35 - 0.05 * math.cos(sine / 25),-0.75)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-5),math.rad(0),math.rad(-20)),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 25),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-5),math.rad(0),math.rad(20)),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 25))*angles(math.rad(10),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(2.5),math.rad(0),math.rad(0)),.1)
RW.C0=clerp(RW.C0,cf(1.45,0.5 + 0.1 * math.cos(sine / 25),0)*angles(math.rad(-15),math.rad(0),math.rad(55)),.1)
LW.C0=clerp(LW.C0,cf(-1.45,0.5 + 0.1 * math.cos(sine / 25),0)*angles(math.rad(-15),math.rad(0),math.rad(-55)),.1)
end
elseif torvel<1 then
Anim="Idle"
if attack==false then
if ModeOfGlitch == 1 then
local snap = math.random(1,10)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(23 + math.random(-5,5)),math.rad(math.random(-5,5)),math.rad(22 + math.random(-5,5))),1)
end
RH.C0=clerp(RH.C0,cf(1,-1 - 0.1 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(-5.5 - 2 * math.cos(sine / 56)),math.rad(-12 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.1 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-6),math.rad(22 - 2 * math.cos(sine / 56)),math.rad(-1 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.01 + 0.03 * math.cos(sine / 32),0 + 0.1 * math.cos(sine / 32))*angles(math.rad(1 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-22 + 2 * math.cos(sine / 56))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(23 - 2 * math.cos(sine / 37)),math.rad(0 + 5 * math.cos(sine / 43) - 5 * math.cos(sine / 0.25)),math.rad(22 - 2 * math.cos(sine / 56))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 + 3 * math.cos(sine / 43)),math.rad(-16 - 5 * math.cos(sine / 52)),math.rad(13 + 9 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.35,1 + 0.025 * math.cos(sine / 45),-0.2)*angles(math.rad(148 - 2 * math.cos(sine / 51)),math.rad(0 - 4 * math.cos(sine / 64)),math.rad(22 - 2 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 676767 then
local snap = math.random(1,10)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(30 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),1)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-14 - 5 * math.cos(sine / 48))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 7 * math.cos(sine / 51))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,6 + 0.5 * math.cos(sine / 24))*angles(math.rad(10 - 0.5 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(30 - 1 * math.cos(sine / 0.5265)),math.rad(0 - 1 * math.cos(sine / 0.25)),math.rad(0 - 1 * math.cos(sine / 0.465))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.3,0.7,0)*angles(math.rad(0 + 1 * math.cos(sine / 0.568)),math.rad(15 - 5 * math.cos(sine / 24)),math.rad(-145 - 15 * math.cos(sine / 24))),.1)
elseif ModeOfGlitch == 2 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-6),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(1 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-0.5),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-1 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.01 + 0.02 * math.cos(sine / 32),0 + 0.05 * math.cos(sine / 32))*angles(math.rad(1 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0 + 3 * math.cos(sine / 42))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(15 - 2 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 58)),math.rad(0 + 1 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 453453484635345 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-20)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(20 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 + 3 * math.cos(sine / 43)),math.rad(-16 - 5 * math.cos(sine / 52)),math.rad(13 + 9 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(90 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(90 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 3 then
local snap = math.random(1,32)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(22 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),1)
end
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(1 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-1 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.02 + 0.02 * math.cos(sine / 32),0 + 0.05 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0 - 1 * math.cos(sine / 44))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(22 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(33 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 146536 then
RH.C0=clerp(RH.C0,cf(1, -1 - 0.025 * math.cos(sine/12), -0.01)*angles(math.rad(0),math.rad(83),math.rad(0))*angles(math.rad(0),math.rad(0),math.rad(0)),0.1)
LH.C0=clerp(LH.C0,cf(-1, -1 - 0.05 * math.cos(sine/12), -0.01)*angles(math.rad(0),math.rad(-83),math.rad(0))*angles(math.rad(0),math.rad(0),math.rad(0)),0.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0, 0, 0 + 0.05 * math.cos(sine / 12))*angles(math.rad(0),math.rad(0),math.rad(0)),0.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*cf(0, 0, 0 + ((1) - 1))*angles(math.rad(15 - 2.5 * math.sin(sine / 12)),math.rad(0),math.rad(0)),0.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.125 * math.cos(sine / 12),-0.45)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.125 * math.cos(sine / 12),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 909090 then --Aesthetic Idle
RH.C0=clerp(RH.C0,cf(1,-1,0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(-60 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(0 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 + 3 * math.cos(sine / 43)),math.rad(-16 - 5 * math.cos(sine / 52)),math.rad(60 + 9 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-0.98,1 + 0.025 * math.cos(sine / 45),0.7)*angles(math.rad(90 - 7 * math.cos(sine / 66)),math.rad(50 - 3 * math.cos(sine / 59)),math.rad(90 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 3434 then -- bacc
RootJoint.C0 = clerp(RootJoint.C0, RootCF * CF(2 + Cos(sine / 70) * Cos(sine / 28) - 2, 0 , 5 + 1* Player_Size * Cos(sine / 24)) * angles(Rad(0 + 0.5 * Cos(sine / 20)), Rad(0), Rad(0)), 0.1)
Torso.Neck.C0 = clerp(Torso.Neck.C0, necko* CF(0, 0, 0 + ((1* Player_Size) - 1)) * angles(Rad(20 - 5 * Sin(sine / 20)), Rad(0), Rad(3 - 3 * Cos(sine / 16))), 0.1)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-14 - 5 * math.cos(sine / 48))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 7 * math.cos(sine / 51))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(33 - 3 * math.cos(sine / 45))),.1)

elseif ModeOfGlitch == 102 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(0),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(1 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1.1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(-7),math.rad(-90),math.rad(0))*angles(math.rad(5 + 2 * math.cos(sine / 32)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-1 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.02 + 0.02 * math.cos(sine / 32),0 + 0.05 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0 - 1 * math.cos(sine / 44))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(22 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 7 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),-0.25)*angles(math.rad(33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.25)*angles(math.rad(23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(33 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 4 then
local snap = math.random(1,5)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(25 + math.random(-1,1)),math.rad(math.random(-1,1)),math.rad(math.random(-1,1))),0.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(25 + 5 * math.cos(sine / 74) + math.random(-1,1)),math.rad(1 - 3 * math.cos(sine / 53) + math.random(-1,1)),math.rad(1 + 3 * math.cos(sine / 45) + math.random(-1,1))),.6)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(25 - 3 * math.cos(sine / 73) + math.random(-1,1)),math.rad(2 - 1 * math.cos(sine / 55) + math.random(-1,1)),math.rad(-3 - 3 * math.cos(sine / 45) + math.random(-1,1))),.6)
end
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(25 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-25 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.25 + 0.02 * math.cos(sine / 32),-0.1 + 0.05 * math.cos(sine / 32))*angles(math.rad(25 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(25 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(25 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(1 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(25 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-3 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 5 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(0 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),-0.1 + 0.05 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-10)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(5 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(10 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(2 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(8 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-14 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 3444 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(8 - 6 * math.cos(sine / 67) - 5 * math.cos(sine / 1)),math.rad(0 - 1 * math.cos(sine / 56) - 5 * math.cos(sine / 1)),math.rad(-18 - 5 * math.cos(sine / 32) - 5 * math.cos(sine / 1))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-15 - 8 * math.cos(sine / 74) - 5 * math.cos(sine / 1)),math.rad(0 - 1 * math.cos(sine / 56) - 5 * math.cos(sine / 1)),math.rad(31 + 8 * math.cos(sine / 38) - 5 * math.cos(sine / 1))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(-21 - 2 * math.cos(sine / 32) - 5 * math.cos(sine / 1)),math.rad(8 - 5 * math.cos(sine / 1)),math.rad(0 - 5 * math.cos(sine / 1))),.1)
Torso.Neck.C0 = clerp(Torso.Neck.C0, necko* cf(0, 0, 0 + ((1) - 1)) * angles(math.rad(15 - 2.5 * math.cos(sine / 30) - 5 * math.cos(sine / 1)), math.rad(20 - 5 * math.cos(sine / 1)), math.rad(0 - 5 * math.cos(sine / 1))), 0.08)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(3 + 7 * math.cos(sine / 79) - 5 * math.cos(sine / 1)),math.rad(1 - 3 * math.cos(sine / 53) - 5 * math.cos(sine / 1)),math.rad(33 + 10 * math.cos(sine / 73) - 5 * math.cos(sine / 1))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(15 - 3 * math.cos(sine / 73) - 5 * math.cos(sine / 1)),math.rad(2 - 1 * math.cos(sine / 55) - 5 * math.cos(sine / 1)),math.rad(-27 - 6 * math.cos(sine / 33) - 5 * math.cos(sine / 1))),.1)
elseif ModeOfGlitch == 090 then --RAINBOW Idle
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-7.5),math.rad(0),math.rad(0 + 1 * math.cos(sine / 34))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-2.5),math.rad(0),math.rad(0 + 1 * math.cos(sine / 34))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0,0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0 - 1 * math.cos(sine / 34)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(5 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(10 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(172 - 7 * math.cos(sine / 58)),math.rad(1 - 5 * math.cos(sine / 55)),math.rad(19 - 3 * math.cos(sine / 45))),.2)
elseif ModeOfGlitch == 102341 then -- Theories --
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.65)*angles(math.rad(-15),math.rad(84),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,-0.2)*angles(math.rad(-10),math.rad(-84),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),2 + 0.15 * math.cos(sine / 32))*angles(math.rad(-5 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(-15 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(20 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(-15 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-24.5 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 664663666 then --verybio
local snap = math.random(1,5)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(25 + math.random(-1,1)),math.rad(math.random(-1,1)),math.rad(math.random(-1,1))),0.6)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(25 + 5 * math.cos(sine / 74) + math.random(-1,1)),math.rad(1 - 3 * math.cos(sine / 53) + math.random(-1,1)),math.rad(1 + 3 * math.cos(sine / 45) + math.random(-1,1))),.6)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.25 + 0.02 * math.cos(sine / 32),1 + 0.1 * math.cos(sine / 32))*angles(math.rad(25 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(25 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(25 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(1 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0)*angles(math.rad(160),math.rad(0),math.rad(25)),.1)
elseif ModeOfGlitch == 6 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 7 * math.cos(sine / 56))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 52))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(2 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(14 + 5 * math.cos(sine / 32))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-14 - 6 * math.cos(sine / 33))),.1)
elseif ModeOfGlitch == 8 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-20)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(20 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 9 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(-4 - 2 * math.cos(sine / 53)),math.rad(0 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(9 - 2 * math.cos(sine / 53)),math.rad(0 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),-0.1 + 0.05 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0 - 2 * math.cos(sine / 53))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(19 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(2 + 5 * math.cos(sine / 74)),math.rad(18 - 3 * math.cos(sine / 53)),math.rad(17 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 - 3 * math.cos(sine / 73)),math.rad(-11 - 1 * math.cos(sine / 55)),math.rad(-14 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 71 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28) - 0.04 * math.cos(sine / 50),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-1 + 4 * math.cos(sine / 50)),math.rad(0),math.rad(0 - 2 * math.cos(sine / 34))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28) + 0.04 * math.cos(sine / 50),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1.5 - 4 * math.cos(sine / 50)),math.rad(18),math.rad(0 + 2 * math.cos(sine / 34))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.04 * math.cos(sine / 50),0 + 0.03 * math.cos(sine / 34),0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0 - 3 * math.cos(sine / 34)),math.rad(0 + 4 * math.cos(sine / 50)),math.rad(18)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 1 * math.cos(sine / 28)),math.rad(-5 - 2.5 * math.cos(sine / 57)),math.rad(-18)),.1)
RW.C0=clerp(RW.C0,cf(0.85,0.5 + 0.05 * math.cos(sine / 28),-0.65)*angles(math.rad(36 - 7 * math.cos(sine / 34)),math.rad(0 - 6 * math.cos(sine / 45)),math.rad(-80 + 2 * math.cos(sine / 28))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(0 + 3 * math.cos(sine / 49)),math.rad(10 + 2 * math.cos(sine / 52)),math.rad(-5 - 3 * math.cos(sine / 39))),.1)
elseif ModeOfGlitch == 8889 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 5 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(-36)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(36 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(3 + 7 * math.cos(sine / 79)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(33 + 10 * math.cos(sine / 73))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(15 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-27 - 6 * math.cos(sine / 33))),.1)
elseif ModeOfGlitch == 88893333388 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 43),0 - 0.25 * math.cos(sine / 53),6 + 1 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(13)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(24 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-13 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(68 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(82 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 103 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),7 + 1 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(13)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(24 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-13 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(68 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(149 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-17 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 808080808080808080808080 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),7 + 1 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(13)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(24 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-13 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(68 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(82 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 6518594185 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),7 + 1 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(13)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(24 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-13 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 14 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(35 - 8 * math.cos(sine / 51))),.1)
elseif ModeOfGlitch == 1264532489 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-14 - 5 * math.cos(sine / 48))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 7 * math.cos(sine / 51))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 43),0 - 0.25 * math.cos(sine / 53),6 + 1 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(21 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(13 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(28 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 101 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-14 - 5 * math.cos(sine / 48))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 7 * math.cos(sine / 51))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 43),0 - 0.25 * math.cos(sine / 53),6 + 1 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(21 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(13 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(28 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(13 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(-23 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 61 then
local snap = math.random(1,4)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(22 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),1)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(20),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-14 - 5 * math.cos(sine / 48))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(20),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 7 * math.cos(sine / 51))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 43),0 - 0.25 * math.cos(sine / 53),6 + 1 * math.cos(sine / 32))*angles(math.rad(20 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(21 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),-0.2)*angles(math.rad(130 + 6 * math.cos(sine / 72) + MRANDOM(-20,20)),math.rad(3 - 2 * math.cos(sine / 58) + MRANDOM(-20,20)),math.rad(-38 + 2 * math.cos(sine / 45) + MRANDOM(-20,20))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.2)*angles(math.rad(130 - 7 * math.cos(sine / 66) + MRANDOM(-20,20)),math.rad(4 - 3 * math.cos(sine / 59) + MRANDOM(-20,20)),math.rad(33 - 4 * math.cos(sine / 45) + MRANDOM(-20,20))),.1)
elseif ModeOfGlitch == 1055 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(8 - 6 * math.cos(sine / 67)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-18 - 5 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-15 - 8 * math.cos(sine / 74)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(31 + 8 * math.cos(sine / 38))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(-21 - 2 * math.cos(sine / 32)),math.rad(8),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(25 - 6 * math.cos(sine / 37)),math.rad(-14 + 5 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(67 - 4 * math.cos(sine / 45))),.1)

elseif ModeOfGlitch == 9999999921111 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(8 - 6 * math.cos(sine / 67)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-18 - 5 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-15 - 8 * math.cos(sine / 74)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(31 + 8 * math.cos(sine / 38))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(-21 - 2 * math.cos(sine / 32)),math.rad(8),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(25 - 6 * math.cos(sine / 37)),math.rad(-14 + 5 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(-24 + 9 * math.cos(sine / 72)),math.rad(3 - 5 * math.cos(sine / 58)),math.rad(38 + 7 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-0.8,0.35 + 0.025 * math.cos(sine / 45),-0.75)*angles(math.rad(160 - 2 * math.cos(sine / 66)),math.rad(5 - 8 * math.cos(sine / 59)),math.rad(87 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 4367677813 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(10)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(15 - 2 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 58)),math.rad(-10 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(4 + 3 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(19 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.25,0.5 + 0.025 * math.cos(sine / 45),-0.15)*angles(math.rad(10 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(13 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 2332 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-6),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(1 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-0.5),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-1 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.01 + 0.02 * math.cos(sine / 32),0 + 0.05 * math.cos(sine / 32))*angles(math.rad(1 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0 + 3 * math.cos(sine / 42))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(15 - 2 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 58)),math.rad(0 + 1 * math.cos(sine / 53))),.1)
LW.C0 = aclerp(LW.C0,CFrame.new(-0.787155986, 0.248306945, -0.683226228, 0.0398273654, -0.999169707, 0.00859495346, 0.554963291, 0.0149663882, -0.831740201, 0.830920994, 0.0378959104, 0.555098593)*angles(math.rad(0+5*math.cos(sine/32)),0,0),Alpha)
RW.C0 = aclerp(RW.C0,CFrame.new(0.787632346, 0.574486911, -0.794373989, 0.0789790228, 0.995851278, 0.0451963581, 0.631366551, -0.0148838377, -0.775341749, -0.771452367, 0.0897712111, -0.629922688)*angles(math.rad(0+5*math.cos(sine/32)),0,0),Alpha)
elseif ModeOfGlitch == 2334 then
rootj.C0 = clerp(rootj.C0, RootCF * CF(0, 0, -0.1 + 0.1 * Cos(sine / 20)) * angles(Rad(0), Rad(0), Rad(0)), 0.15)
tors.Neck.C0 = clerp(tors.Neck.C0, necko * angles(Rad(-20), Rad(0), Rad(0)), 0.3)
RH.C0 = clerp(RH.C0, CF(1, -0.9 - 0.1 * Cos(sine / 20), 0.025 * Cos(sine / 20)) * RHCF * angles(Rad(-2.5), Rad(0), Rad(0)), 0.15)
LH.C0 = clerp(LH.C0, CF(-1, -0.9 - 0.1 * Cos(sine / 20), 0.025 * Cos(sine / 20)) * LHCF * angles(Rad(-2.5), Rad(0), Rad(0)), 0.15)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(3 - 2 * math.cos(sine / 58)),math.rad(-82 + 2 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-0.95,0.65 + 0.075 * math.cos(sine / 28),-0.65)*angles(math.rad(90 + 2 * math.cos(sine / 73)),math.rad(25 + 5 * math.cos(sine / 24)),math.rad(73 - 3 * math.cos(sine / 65))),.1)
elseif ModeOfGlitch == 666666 then
local snap = math.random(1,30)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 + math.random(-45,0)),math.rad(math.random(-45,45)),math.rad(math.random(-45,45))),1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.4 * math.cos(sine / 30),0 + 0.4 * math.cos(sine / 32),5.2 + 0.4 * math.cos(sine / 26))*angles(math.rad(20 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(math.random(-5,5))),1)
RW.C0=clerp(RW.C0,cf(1.35,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(math.random(40,185) + 6 * math.cos(sine / 72)),math.rad(2 - 4 * math.cos(sine / 58)),math.rad(math.random(-35,-7) + 1 * math.cos(sine / 45))),1)
LW.C0=clerp(LW.C0,cf(-1.35,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(math.random(40,187) - 7 * math.cos(sine / 66)),math.rad(6 - 5 * math.cos(sine / 59)),math.rad(math.random(9,31) - 3 * math.cos(sine / 45))),1)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(-15 - 5 * math.cos(sine / 32)),math.rad(100- 5 * math.cos(sine / 32)),math.rad(0))*angles(math.rad(-3 + math.random(-5,5)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(-15 - 2.5 * math.cos(sine / 32)),math.rad(-95),math.rad(0))*angles(math.rad(-3 + math.random(-5,5)),math.rad(0 - 1 * math.cos(sine / 56)+ math.random(-5,5)),math.rad(10 + 6 * math.cos(sine / 31) + math.random(-5,5))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.2 * math.cos(sine / 30),0 + 0.2 * math.cos(sine / 34),5.2 + 0.4 * math.cos(sine / 26))*angles(math.rad(0 - 2 * math.cos(sine / 32) + math.random(-5,5)),math.rad(0 + math.random(-5,5)),math.rad(math.random(-5,5))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37) + math.random(-5,5)),math.rad(0 + 1 * math.cos(sine / 58) + math.random(-5,5)),math.rad(0 + 2 * math.cos(sine / 53) + math.random(-5,5))),.1)
RW.C0=clerp(RW.C0,cf(0.9,0.5 + 0.1 * math.cos(sine / 34),-0.5)*angles(math.rad(0 + math.random(-5,5)),math.rad(-20 - 7.5 * math.cos(sine / 26) + math.random(-5,5)),math.rad(-90 - 5 * math.cos(sine / 34) + math.random(-5,5))),.3)
LW.C0=clerp(LW.C0,cf(-0.9,0.4 + 0.1 * math.cos(sine / 34),-0.5)*angles(math.rad(0 + math.random(-5,5)),math.rad(20 + 7.5 * math.cos(sine / 32) + math.random(-5,5)),math.rad(87.5 + 5 * math.cos(sine / 34) + math.random(-5,5))),.3)

elseif ModeOfGlitch == 765688533321 then
local snap = math.random(1,12)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),1)
end
local snap2 = math.random(1,32)
if snap2 == 1 then
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 50),0 + 0.25 * math.cos(sine / 43),6 + 1 * math.cos(sine / 32))*angles(math.rad(20 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),1)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 - 2 * math.cos(sine / 39))),1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 + 6 * math.cos(sine / 31))),1)
RW.C0=clerp(RW.C0,cf(1.5,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(185 + 6 * math.cos(sine / 72)),math.rad(2 - 4 * math.cos(sine / 58)),math.rad(-12 + 1 * math.cos(sine / 45))),1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(185 - 7 * math.cos(sine / 66)),math.rad(6 - 5 * math.cos(sine / 59)),math.rad(9 - 3 * math.cos(sine / 45))),1)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 6 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 50),0 + 0.25 * math.cos(sine / 43),6 + 1 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72)),math.rad(2 - 4 * math.cos(sine / 58)),math.rad(-65 + 1 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(89 - 7 * math.cos(sine / 66)),math.rad(6 - 5 * math.cos(sine / 59)),math.rad(73 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 55469696922 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-14 - 5 * math.cos(sine / 48))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 7 * math.cos(sine / 51))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 50),0 + 0.25 * math.cos(sine / 43),6 + 1 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(30 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 14 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(35 - 8 * math.cos(sine / 51))),.1)
elseif ModeOfGlitch == 343434 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-2.5),math.rad(-10 + 2 * math.cos(sine / 43)),math.rad(0 - 2 * math.cos(sine / 34))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 28),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-1.5),math.rad(0),math.rad(0 + 2 * math.cos(sine / 34))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 34),0 + 0.05 * math.cos(sine / 28))*angles(math.rad(0 - 2 * math.cos(sine / 34)),math.rad(0),math.rad(10 - 2 * math.cos(sine / 43))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(5 - 2.5 * math.cos(sine / 28)),math.rad(0 - 2 * math.cos(sine / 47)),math.rad(-10 + 2 * math.cos(sine / 43))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(10 + 3 * math.cos(sine / 48)),math.rad(-20 - 4 * math.cos(sine / 53)),math.rad(15 - 3 * math.cos(sine / 38))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(-10 + 2 * math.cos(sine / 45)),math.rad(0),math.rad(-20 + 2 * math.cos(sine / 39))),.1)
elseif ModeOfGlitch == 343435 then -- Lunar --
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-4 - 7 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 8 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),7 + 1 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(13)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(5 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(10 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.3+.2*math.cos(sine/32),-.65)*angles(math.rad(60),0,math.rad(-90)),.1)
LW.C0=clerp(LW.C0,cf(-1,0.6+.2*math.cos(sine/32),-.65)*angles(math.rad(40),0,math.rad(90)),.1)
elseif ModeOfGlitch == 999 then
sphere2(2.5,"Add",rleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,-0.01,-0.01,MAINRUINCOLOR,MAINRUINCOLOR.Color)
sphere2(2.5,"Add",lleg.CFrame*CFrame.new(0,-1,0)*CFrame.Angles(math.rad(math.random(-360,360)),math.rad(math.random(-360,360)),math.rad(math.random(-360,360))),vt(1,1,1),-0.01,-0.01,-0.01,MAINRUINCOLOR,MAINRUINCOLOR.Color)
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),3 + 1 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(13)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(24 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-13 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(2 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(8 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-14 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 664663666 then
local snap = math.random(1,32)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(32 + math.random(-10,10)),math.rad(math.random(-10,10)),math.rad(math.random(-10,10))),1)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-4 - 7 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(15 + 8 * math.cos(sine / 31))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0 + 0.02 * math.cos(sine / 32),1 + 0.15 * math.cos(sine / 32))*angles(math.rad(32 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(30 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(32 + 6 * math.cos(sine / 72)),math.rad(2 - 4 * math.cos(sine / 58)),math.rad(14 + 1 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(32 - 7 * math.cos(sine / 66)),math.rad(6 - 5 * math.cos(sine / 59)),math.rad(-9 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 1236 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(3),math.rad(0 + 3 * math.cos(sine / 56)),math.rad(1 - 2 * math.cos(sine / 32))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.05 * math.cos(sine / 32),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(6),math.rad(0 + 3 * math.cos(sine / 56)),math.rad(3 + 2 * math.cos(sine / 32))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,0.02 + 0.02 * math.cos(sine / 32),0 + 0.05 * math.cos(sine / 32))*angles(math.rad(2 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0 - 6 * math.cos(sine / 56))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-3 - 5 * math.cos(sine / 37)),math.rad(0 + 14 * math.cos(sine / 58)),math.rad(0 + 3 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),-0.45)*angles(math.rad(33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.45)*angles(math.rad(23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(33 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 999999999556 then
local snap = math.random(1,8)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(32 + math.random(-20,20)),math.rad(math.random(-20,20)),math.rad(math.random(-20,20))),1)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3 + MRANDOM(-3,3)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 6 * math.cos(sine / 39) + MRANDOM(-5,5))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3 + MRANDOM(-3,3)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 3 * math.cos(sine / 45) + MRANDOM(-5,5))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.25 * math.cos(sine / 47),0 - 0.25 * math.cos(sine / 40),7 + 1 * math.cos(sine / 32))*angles(math.rad(0 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(17)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(29 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-17 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.35 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(62 + 6 * math.cos(sine / 72) + MRANDOM(-10,10)),math.rad(3 - 2 * math.cos(sine / 58) + MRANDOM(-20,20)),math.rad(-82 + 2 * math.cos(sine / 45) + MRANDOM(-20,20))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(99 - 7 * math.cos(sine / 66) + MRANDOM(-20,20)),math.rad(4 - 3 * math.cos(sine / 59) + MRANDOM(-20,20)),math.rad(67 - 4 * math.cos(sine / 45) + MRANDOM(-20,20))),.1)
end
end
elseif torvel>2 and torvel<22 then
Anim="Walk"
if attack==false then
if ModeOfGlitch ~= 102 and ModeOfGlitch ~= 1236 and ModeOfGlitch ~= 2334 then
RH.C0=clerp(RH.C0,cf(1,-1 + 0.05 * math.cos(sine / 4),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(0),math.rad(0 + 5 * math.cos(sine / 8)),math.rad(0 + 35 * math.cos(sine / 8))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 + 0.05 * math.cos(sine / 4),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(0),math.rad(0 + 5 * math.cos(sine / 8)),math.rad(0 + 35 * math.cos(sine / 8))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.05,-0.05 - 0.05 * math.cos(sine / 4))*angles(math.rad(5 + 3 * math.cos(sine / 4)),math.rad(0 + root.RotVelocity.Y/1.5),math.rad(0 - root.RotVelocity.Y - 5 * math.cos(sine / 8))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 3 * math.cos(sine / 4)),math.rad(0 + root.RotVelocity.Y/1.5),math.rad(0 - hed.RotVelocity.Y*1.5 + 5 * math.cos(sine / 8))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0 + 0.25 * math.cos(sine / 8))*angles(math.rad(0 - 50 * math.cos(sine / 8)),math.rad(0),math.rad(5 - 10 * math.cos(sine / 4))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0 - 0.25 * math.cos(sine / 8))*angles(math.rad(0 + 50 * math.cos(sine / 8)),math.rad(0),math.rad(-5 + 10 * math.cos(sine / 4))),.1)
elseif ModeOfGlitch == 2334 then
rootj.C0 = clerp(rootj.C0, RootCF * CF(0, 0, -0.175 + 0.025 * Cos(sine / 3.5) + -Sin(sine / 3.5) / 7) * angles(Rad(9-2.5 * Cos(sine / 3.5)), Rad(0), Rad(10 * Cos(sine / 7))), 0.15)
tors.Neck.C0 = clerp(tors.Neck.C0, necko * angles(Rad(0), Rad(0), Rad(0)), 0.3)
RH.C0 = clerp(RH.C0, CFrame.new(1, -0.925 - 0.5 * math.cos(sine / 7) / 2, 0.5 * math.cos(sine / 7) / 2) * angles(math.rad(-15 - 35 * math.cos(sine / 7)) + -math.sin(sine / 7) / 2.5, math.rad(90 - 2 * math.cos(sine / 7)), math.rad(0)) * angles(math.rad(0 + 2.5 * math.cos(sine / 7)), math.rad(0), math.rad(0)), 0.3)
LH.C0 = clerp(LH.C0, CFrame.new(-1, -0.925 + 0.5 * math.cos(sine / 7) / 2, -0.5 * math.cos(sine / 7) / 2) * angles(math.rad(-15 + 35 * math.cos(sine / 7)) + math.sin(sine / 7) / 2.5, math.rad(-90 - 2 * math.cos(sine / 7)), math.rad(0)) * angles(math.rad(0 - 2.5 * math.cos(sine / 7)), math.rad(0), math.rad(0)), 0.3)
RW.C0 = clerp(RW.C0, CF(1.5, 0.5 + 0.05 * Sin(sine / 30), 0.025 * Cos(sine / 20)) * angles(Rad(70) * Cos(sine / 7) , Rad(0), Rad(5)), 0.1)
LW.C0 = clerp(LW.C0, CF(-1.5, 0.5 + 0.05 * Sin(sine / 30), 0.025 * Cos(sine / 20)) * angles(Rad(-70) * Cos(sine / 7) , Rad(0),	Rad(-5)), 0.1)
elseif ModeOfGlitch == 102 or ModeOfGlitch == 1236 then
RH.C0=clerp(RH.C0,cf(1,-1 + 0.05 * math.cos(sine / 4),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(0),math.rad(0 + 5 * math.cos(sine / 8)),math.rad(0 + 35 * math.cos(sine / 8))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 + 0.05 * math.cos(sine / 4),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(0),math.rad(0 + 5 * math.cos(sine / 8)),math.rad(0 + 35 * math.cos(sine / 8))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.05,-0.05 - 0.05 * math.cos(sine / 4))*angles(math.rad(5 + 3 * math.cos(sine / 4)),math.rad(0 + root.RotVelocity.Y/1.5),math.rad(0 - root.RotVelocity.Y - 5 * math.cos(sine / 8))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(10 - 3 * math.cos(sine / 4)),math.rad(0 + root.RotVelocity.Y/1.5),math.rad(0 - hed.RotVelocity.Y*1.5 + 5 * math.cos(sine / 8))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),-0.25)*angles(math.rad(33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.25)*angles(math.rad(23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(33 - 3 * math.cos(sine / 45))),.1)
end
end
elseif torvel>=22 then
Anim="Run"
if attack==false then
if ModeOfGlitch ~= 6 and ModeOfGlitch ~= 666666 and ModeOfGlitch ~= 343435 and ModeOfGlitch ~= 909090 and ModeOfGlitch ~= 102341 and ModeOfGlitch ~= 1055 and ModeOfGlitch ~= 6518594185 and ModeOfGlitch ~= 61 and ModeOfGlitch ~= 676767 and ModeOfGlitch ~= 3434 and ModeOfGlitch ~= 453453484635345 and ModeOfGlitch ~= 103 and ModeOfGlitch ~= 999 and ModeOfGlitch ~= 3444 and ModeOfGlitch ~= 101 and ModeOfGlitch ~= 8 and ModeOfGlitch ~= 1264532489 and ModeOfGlitch ~= 55469696922 and ModeOfGlitch ~= 4367677813 and ModeOfGlitch ~= 9999999921111 and ModeOfGlitch ~= 999999999556 and ModeOfGlitch ~= 8889 and ModeOfGlitch ~= 765688533321 and ModeOfGlitch ~= 664663666 and ModeOfGlitch ~= 88893333388 and ModeOfGlitch ~= 808080808080808080808080 then
RH.C0=clerp(RH.C0,cf(1,-1 - 0.15 * math.cos(sine / 3),0)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(0),math.rad(0),math.rad(0 + 95 * math.cos(sine / 6))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1 - 0.15 * math.cos(sine / 3),0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(0),math.rad(0),math.rad(0 + 95 * math.cos(sine / 6))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0,-0.3,-0.05 + 0.15 * math.cos(sine / 3))*angles(math.rad(25 - 4 * math.cos(sine / 3)),math.rad(0 + root.RotVelocity.Y*1.5),math.rad(0 - root.RotVelocity.Y - 1 * math.cos(sine / 6))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-6 + 2 * math.cos(sine / 3)),math.rad(0 + root.RotVelocity.Y*1.5),math.rad(0 - hed.RotVelocity.Y*1.5 + 1 * math.cos(sine / 6))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5,0.3)*angles(math.rad(-50 + 10 * math.cos(sine / 3)),math.rad(-10),math.rad(7 + 5 * math.cos(sine / 6))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5,0.3)*angles(math.rad(-50 + 10 * math.cos(sine / 3)),math.rad(10),math.rad(-7 - 5 * math.cos(sine / 6))),.1)
elseif ModeOfGlitch == 102341 then -- theories 
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20 - 5 * math.cos(sine / 34))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20 + 2 * math.cos(sine / 38))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.15 * math.cos(sine / 47),-0.5,0.5 + 0.1 * math.cos(sine / 28))*angles(math.rad(70),math.rad(0 - root.RotVelocity.Y),math.rad(0 - root.RotVelocity.Y *4.5 + 3 * math.cos(sine / 47))),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17 - 5 * math.cos(sine / 52)),math.rad(0 - 3 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 78))),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-8 - 4 * math.cos(sine / 59)),math.rad(-20 + 7 * math.cos(sine / 62)),math.rad(20 + 5 * math.cos(sine / 50))),.2)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(-8 - 3 * math.cos(sine / 55)),math.rad(20 + 8 * math.cos(sine / 67)),math.rad(-20 - 4 * math.cos(sine / 29))),.2)
elseif ModeOfGlitch == 666666 then
local snap = math.random(1,30)
if snap == 1 then
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 + math.random(-45,0)),math.rad(math.random(-45,45)),math.rad(math.random(-45,45))),1)
end
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(-15 - 5 * math.cos(sine / 32)),math.rad(100- 5 * math.cos(sine / 32)),math.rad(0))*angles(math.rad(-3 + math.random(-5,5)),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 2 * math.cos(sine / 39))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(-15 - 2.5 * math.cos(sine / 32)),math.rad(-95),math.rad(0))*angles(math.rad(-3 + math.random(-5,5)),math.rad(0 - 1 * math.cos(sine / 56)+ math.random(-5,5)),math.rad(10 + 6 * math.cos(sine / 31) + math.random(-5,5))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.2 * math.cos(sine / 30),0 + 0.2 * math.cos(sine / 34),5.2 + 0.4 * math.cos(sine / 26))*angles(math.rad(50 - 2 * math.cos(sine / 32) + math.random(-5,5)),math.rad(0 + math.random(-5,5)),math.rad(math.random(-5,5))),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(13 - 2 * math.cos(sine / 37) + math.random(-5,5)),math.rad(0 + 1 * math.cos(sine / 58) + math.random(-5,5)),math.rad(0 + 2 * math.cos(sine / 53) + math.random(-5,5))),.1)
RW.C0=clerp(RW.C0,cf(0.9,0.5 + 0.1 * math.cos(sine / 34),-0.5)*angles(math.rad(0 + math.random(-5,5)),math.rad(-20 - 7.5 * math.cos(sine / 26) + math.random(-5,5)),math.rad(-90 - 5 * math.cos(sine / 34) + math.random(-5,5))),.3)
LW.C0=clerp(LW.C0,cf(-0.9,0.4 + 0.1 * math.cos(sine / 34),-0.5)*angles(math.rad(0 + math.random(-5,5)),math.rad(20 + 7.5 * math.cos(sine / 32) + math.random(-5,5)),math.rad(87.5 + 5 * math.cos(sine / 34) + math.random(-5,5))),.3)
elseif ModeOfGlitch == 6518594185 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),7 + 1 * math.cos(sine / 32))*angles(math.rad(28 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-24 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(0 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-33 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(-33 + 14 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),0.45)*angles(math.rad(-23 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(35 - 8 * math.cos(sine / 51))),.1)
elseif ModeOfGlitch == 999 then
RH.C0=clerp(RH.C0,cf(1,-0.4,-0.5)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(-10 - 9 * math.cos(sine / 51))),.1)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(-3),math.rad(0 - 1 * math.cos(sine / 56)),math.rad(10 + 7 * math.cos(sine / 44))),.1)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 + 0.25 * math.cos(sine / 47),0 + 0.25 * math.cos(sine / 35),3 + 1 * math.cos(sine / 32))*angles(math.rad(34 - 2 * math.cos(sine / 32)),math.rad(0),math.rad(0)),.1)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(0 - 2 * math.cos(sine / 37)),math.rad(0 + 1 * math.cos(sine / 58)),math.rad(-13 + 2 * math.cos(sine / 53))),.1)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(2 + 5 * math.cos(sine / 74)),math.rad(1 - 3 * math.cos(sine / 53)),math.rad(8 + 3 * math.cos(sine / 45))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),0)*angles(math.rad(5 - 3 * math.cos(sine / 73)),math.rad(2 - 1 * math.cos(sine / 55)),math.rad(-14 - 3 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 6 or ModeOfGlitch == 3444 or ModeOfGlitch == 8 or ModeOfGlitch == 4367677813 or ModeOfGlitch == 9999999921111 or ModeOfGlitch == 8889 or ModeOfGlitch == 664663666  then
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20 - 5 * math.cos(sine / 34))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20 + 2 * math.cos(sine / 38))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.15 * math.cos(sine / 47),-0.5,1 + 0.25 * math.cos(sine / 28))*angles(math.rad(70),math.rad(0 - root.RotVelocity.Y),math.rad(0 - root.RotVelocity.Y *4.5 + 3 * math.cos(sine / 47))),.05)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17 - 5 * math.cos(sine / 52)),math.rad(0 - 3 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 78))),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-8 - 4 * math.cos(sine / 59)),math.rad(-20 + 7 * math.cos(sine / 62)),math.rad(20 + 5 * math.cos(sine / 50))),.2)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(-8 - 3 * math.cos(sine / 55)),math.rad(20 + 8 * math.cos(sine / 67)),math.rad(-20 - 4 * math.cos(sine / 29))),.2)
elseif ModeOfGlitch == 453453484635345 then
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20 - 5 * math.cos(sine / 34))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20 + 2 * math.cos(sine / 38))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.15 * math.cos(sine / 47),-0.5,1 + 0.25 * math.cos(sine / 28))*angles(math.rad(70),math.rad(0 - root.RotVelocity.Y),math.rad(0 - root.RotVelocity.Y *4.5 + 3 * math.cos(sine / 47))),.05)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-50 - 5 * math.cos(sine / 52)),math.rad(0 - 3 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 78))),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-20 - 20 * math.cos(sine / 59)),math.rad(-20 + 7 * math.cos(sine / 62)),math.rad(110 + 5 * math.cos(sine / 50))),.2)
LW.C0=clerp(LW.C0,cf(-1,0.5 + 0.025 * math.cos(sine / 45),-0.5)*angles(math.rad(90 - 7 * math.cos(sine / 66)),math.rad(4 - 3 * math.cos(sine / 59)),math.rad(90 - 4 * math.cos(sine / 45))),.1)
elseif ModeOfGlitch == 103 then
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20 - 5 * math.cos(sine / 34))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20 + 2 * math.cos(sine / 38))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.15 * math.cos(sine / 47),-0.5,6.5 + 0.25 * math.cos(sine / 28))*angles(math.rad(70),math.rad(0 - root.RotVelocity.Y),math.rad(0 - root.RotVelocity.Y *4.5 + 3 * math.cos(sine / 47))),.05)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17 - 5 * math.cos(sine / 52)),math.rad(0 - 3 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 78))),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-8 - 4 * math.cos(sine / 59)),math.rad(-20 + 7 * math.cos(sine / 62)),math.rad(20 + 5 * math.cos(sine / 50))),.2)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(-8 - 3 * math.cos(sine / 55)),math.rad(20 + 8 * math.cos(sine / 67)),math.rad(-20 - 4 * math.cos(sine / 29))),.2)
elseif ModeOfGlitch == 1055 then
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20 - 5 * math.cos(sine / 34))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20 + 2 * math.cos(sine / 38))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.15 * math.cos(sine / 47),-0.5,1 + 0.25 * math.cos(sine / 28))*angles(math.rad(40),math.rad(0 - root.RotVelocity.Y),math.rad(0 - root.RotVelocity.Y *4.5 + 3 * math.cos(sine / 47))),.05)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17 - 5 * math.cos(sine / 52)),math.rad(0 - 3 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 78))),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-8 - 4 * math.cos(sine / 59)),math.rad(-20 + 7 * math.cos(sine / 62)),math.rad(20 + 5 * math.cos(sine / 50))),.2)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(-8 - 3 * math.cos(sine / 55)),math.rad(20 + 8 * math.cos(sine / 67)),math.rad(-20 - 4 * math.cos(sine / 29))),.2)
elseif ModeOfGlitch == 909090 then
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20 - 5 * math.cos(sine / 34))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20 + 2 * math.cos(sine / 38))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.15 * math.cos(sine / 47),-0.5,0.5 + 0.1 * math.cos(sine / 12))*angles(math.rad(40),math.rad(0 - root.RotVelocity.Y),math.rad(0 - root.RotVelocity.Y *4.5 + 3 * math.cos(sine / 47))),.2)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17 - 5 * math.cos(sine / 52)),math.rad(0 - 3 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 78))),.2)
RW.C0=clerp(RW.C0,cf(1.4,0.5 + 0.025 * math.cos(sine / 45),-0.1)*angles(math.rad(160 + 3 * math.cos(sine / 74)),math.rad(0 - 1 * math.cos(sine / 53)),math.rad(-15 + 5 * math.cos(sine / 32))),.1)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.025 * math.cos(sine / 45),-0)*angles(math.rad(-5 - 5 * math.cos(sine / 73)),math.rad(0 - 2.25 * math.cos(sine / 55)),math.rad(-15 - 6 * math.cos(sine / 33))),.1)
elseif ModeOfGlitch == 1264532489 or ModeOfGlitch == 88893333388 or ModeOfGlitch == 343435 or ModeOfGlitch == 61 or ModeOfGlitch == 3434 or ModeOfGlitch == 676767 or ModeOfGlitch == 101 or ModeOfGlitch == 999999999556 or ModeOfGlitch == 765688533321 or ModeOfGlitch == 55469696922 or ModeOfGlitch == 808080808080808080808080 then
RH.C0=clerp(RH.C0,cf(1,-0.5,-0.6)*angles(math.rad(0),math.rad(90),math.rad(0))*angles(math.rad(1.5),math.rad(0),math.rad(-20 - 5 * math.cos(sine / 34))),.2)
LH.C0=clerp(LH.C0,cf(-1,-1,0)*angles(math.rad(0),math.rad(-90),math.rad(0))*angles(math.rad(1),math.rad(0),math.rad(20 + 2 * math.cos(sine / 38))),.2)
RootJoint.C0=clerp(RootJoint.C0,RootCF*cf(0 - 0.15 * math.cos(sine / 47),-0.5,6.5 + 0.25 * math.cos(sine / 28))*angles(math.rad(70),math.rad(0 - root.RotVelocity.Y),math.rad(0 - root.RotVelocity.Y *4.5 + 3 * math.cos(sine / 47))),.05)
Torso.Neck.C0=clerp(Torso.Neck.C0,necko*angles(math.rad(-17 - 5 * math.cos(sine / 52)),math.rad(0 - 3 * math.cos(sine / 37)),math.rad(0 + 2 * math.cos(sine / 78))),.2)
RW.C0=clerp(RW.C0,cf(1.5,0.5 + 0.05 * math.cos(sine / 28),0)*angles(math.rad(-8 - 4 * math.cos(sine / 59)),math.rad(-20 + 7 * math.cos(sine / 62)),math.rad(20 + 5 * math.cos(sine / 50))),.2)
LW.C0=clerp(LW.C0,cf(-1.5,0.5 + 0.1 * math.cos(sine / 28),0)*angles(math.rad(-8 - 3 * math.cos(sine / 55)),math.rad(20 + 8 * math.cos(sine / 67)),math.rad(-20 - 4 * math.cos(sine / 29))),.2)
end
end
end
end
end
end))
