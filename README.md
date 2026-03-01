
local function getUrl(url)
	url = url.Url or url
	return type(url) == "string" and url or nil
end

local function verifyUrl(url)
	local lower = url:lower()
	return lower:sub(1, 7) == "http://" or lower:sub(1, 8) == "https://"
end

local function wrap(func, method)
	if type(func) ~= "function" then return end
	return function(...)
		local packed = { ... }
		local url = method and getUrl(packed[2]) or getUrl(packed[1])
		if url then
			if verifyUrl(url) then
				local success, result = pcall(function() return func(table.unpack(packed)) end)
				if success then
					return result
				else
					error(result, method and 3 or 2)
				end
			else
				error("URL must be http", method and 3 or 2)
			end
		else
			error("invalid url", method and 3 or 2)
		end
	end
end


local env = getgenv()
if type(env.request) == "function" then env.request = wrap(env.request) end
if type(env.http_request) == "function" then env.http_request = wrap(env.http_request) end
if type(env.http) == "table" and type(env.http.request) == "function" then
	env.http.request = wrap(env.http.request)
end
if type(env.HttpGet) == "function" then env.HttpGet = wrap(env.HttpGet) end
if type(env.HttpGetAsync) == "function" then env.HttpGetAsync = wrap(env.HttpGetAsync) end


local wrappedHttpGet = wrap(game.HttpGet, true)
local wrappedHttpGetAsync = wrap(game.HttpGetAsync, true)

local mt = getrawmetatable(game)
local oldnc = mt.__namecall
setreadonly(mt, false)
mt.__namecall = function(...)
	local method = getnamecallmethod()
	if checkcaller() then
		if method == "HttpGet" then
			return wrappedHttpGet(...)
		elseif method == "HttpGetAsync" then
			return wrappedHttpGetAsync(...)
		end
	end
	return oldnc(...)
end
setreadonly(mt, true)

local function scanForBackdoors()
    local backdoors = {}
    for _, obj in pairs(ReplicatedStorage:GetDescendants()) do
        if obj:IsA("RemoteEvent") or obj:IsA("RemoteFunction") then
            table.insert(backdoors, obj)
        end
    end
    return backdoors
end

wait(0.2)

Player = game:GetService("Players").LocalPlayer
PlayerGui = Player.PlayerGui
Cam = workspace.CurrentCamera
Backpack = Player.Backpack
Character = Player.Character
Humanoid = Character.Humanoid
Mouse = Player:GetMouse()
RootPart = Character["HumanoidRootPart"]
Torso = Character["Torso"]
Head = Character["Head"]
RightArm = Character["Right Arm"]
LeftArm = Character["Left Arm"]
RightLeg = Character["Right Leg"]
LeftLeg = Character["Left Leg"]
RootJoint = RootPart["RootJoint"]
Neck = Torso["Neck"]
RightShoulder = Torso["Right Shoulder"]
LeftShoulder = Torso["Left Shoulder"]
RightHip = Torso["Right Hip"]
LeftHip = Torso["Left Hip"]
local TIME = 0
local sick = Instance.new("Sound",Torso)
sick.Parent = Torso
sick.Playing = true
sick.Looped = true
sick.Volume = 5
sick.Pitch = 1.14
sick.TimePosition = 0
sick.SoundId = "rbxassetid://76471063800650"

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

--//=================================\\
--|| 	      USEFUL VALUES
--\\=================================//

Animation_Speed = 3
local FORCERESET = false
Frame_Speed = 1 / 60 -- (1 / 30) OR (1 / 60)
local SIZE = 1
local Speed = 16
local ROOTC0 = CF(0, 0, 0) * ANGLES(RAD(-90), RAD(0), RAD(180))
local NECKC0 = CF(0, 1, 0) * ANGLES(RAD(-90), RAD(0), RAD(180))
local RIGHTSHOULDERC0 = CF(-0.5, 0, 0) * ANGLES(RAD(0), RAD(90), RAD(0))
local LEFTSHOULDERC0 = CF(0.5, 0, 0) * ANGLES(RAD(0), RAD(-90), RAD(0))
local DAMAGEMULTIPLIER = 1
local ANIM = "Idle"
local ATTACK = false
local EQUIPPED = false
local HOLD = false
local COMBO = 1
local Rooted = false
local SINE = 0
local KEYHOLD = false
local CHANGE = 2 / Animation_Speed
local WALKINGANIM = false
local VALUE1 = false
local VALUE2 = false
local ROBLOXIDLEANIMATION = IT("Animation")
ROBLOXIDLEANIMATION.Name = "Roblox Idle Animation"
ROBLOXIDLEANIMATION.AnimationId = "http://www.roblox.com/asset/?id=180435571"
--ROBLOXIDLEANIMATION.Parent = Humanoid
local WEAPONGUI = IT("ScreenGui", PlayerGui)
WEAPONGUI.Name = "BanishV3Gui"
local Weapon = IT("Folder", Character)
Weapon.Name = "Weapon"
local Weapon2 = IT("Folder", Character)
Weapon2.Name = "Weapon2"
local DeadEye = IT("Folder", Character)
DeadEye.Name = "DeadEye"
local HM = IT("Folder", Character)
HM.Name = "HM"
local Pan = IT("Folder", Character)
Pan.Name = "Pan"
local Pan2 = IT("Folder", Character)
Pan2.Name = "Pan2"
local FaceGradient = IT("Folder", Character)
FaceGradient.Name = "FaceGradient"
local black = IT("Folder", Character)
black.Name = "black"
local Heads = IT("Folder", Character)
Heads.Name = "Heads"
local Effects = IT("Folder", Character)
Effects.Name = "Effects"
local ANIMATOR = Humanoid.Animator
local ANIMATE = Character:FindFirstChild("Animate")
local UNANCHOR = true
local CLOCKLOOP = 0
local CLOCKTARGET = nil
local CLOCKSPEED = 0
script.Parent = WEAPONGUI
local HITFLOOR = nil
local INTRO = false
local INSTANT = false
local LEFTWINGS = {}
local RIGHTWINGS = {}
local BODY = {}
local TOBANISH = {}
script.Parent = PlayerGui
local MODE = "GoodCop"

--//=================================\\
--\\=================================//

local plr = Player
local char = plr.Character
local hum = char.Humanoid

-------------------------------------------------------
--Start Good Stuff--
-------------------------------------------------------
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

--//=================================\\
--|| SAZERENOS' ARTIFICIAL HEARTBEAT
--\\=================================//

ArtificialHB = Instance.new("BindableEvent", script)
ArtificialHB.Name = "ArtificialHB"

script:WaitForChild("ArtificialHB")

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

--//=================================\\
--\\=================================//

--//=================================\\
--||		  CUSTOMIZATION
--\\=================================//

	local function weldBetween(a, b)
	    local weldd = Instance.new("ManualWeld")
	    weldd.Part0 = a
	    weldd.Part1 = b
	    weldd.C0 = CFrame.new()
	    weldd.C1 = b.CFrame:inverse() * a.CFrame
	    weldd.Parent = a
	    return weldd
	end

function createaccessory(attachmentpart,mesh,texture,scale,offset,color)
local acs = Instance.new("Part")
acs.CanCollide = false
acs.Anchored = false
acs.Size = Vector3.new(0,0,0)
acs.CFrame = attachmentpart.CFrame
acs.Parent = Character
acs.BrickColor = color
    local meshs = Instance.new("SpecialMesh")
    meshs.MeshId = mesh
    meshs.TextureId = texture
    meshs.Parent = acs
    meshs.Scale = scale
    meshs.Offset = offset
weldBetween(attachmentpart,acs)
end

function createbodypart(TYPE,COLOR,PART,OFFSET,SIZE)
if TYPE == "Gem" then
	local acs = CreatePart(3, PART, "Plastic", 0, 0, COLOR, "Part", VT(0,0,0))
	acs.Anchored = false
    acs.CanCollide = false
	acs.CFrame = PART.CFrame
	local acs2 = CreateMesh("SpecialMesh", acs, "FileMesh", "9756362", "", SIZE, OFFSET)
weldBetween(PART,acs)
elseif TYPE == "Skull" then
	local acs = CreatePart(3, PART, "Plastic", 0, 0, COLOR, "Part", VT(0,0,0))
	acs.Anchored = false
    acs.CanCollide = false
	acs.CFrame = PART.CFrame
	local acs2 = CreateMesh("SpecialMesh", acs, "FileMesh", "4770583", "", SIZE, OFFSET)
weldBetween(PART,acs)
elseif TYPE == "Eye" then
	local acs = CreatePart(3, PART, "Neon", 0, 0, COLOR, "Part", VT(0,0,0))
	acs.Anchored = false
    acs.CanCollide = false
	acs.CFrame = PART.CFrame
	local acs2 = CreateMesh("SpecialMesh", acs, "Sphere", "", "", SIZE, OFFSET)
weldBetween(PART,acs)
end
end


Class_Name = "Sin Unleashed"
Weapon_Name = "Add-ons"

Custom_Colors = {
	Custom_Color_1 = BRICKC("Institutional white"); --1st color for the weapon.
	Custom_Color_2 = BRICKC("Institutional white"); --2nd color for the weapon.

	Custom_Color_3 = BRICKC("Institutional white"); --Color for the abilities.
	Custom_Color_4 = BRICKC("Institutional white"); --Color for the secondary bar.
	Custom_Color_5 = BRICKC("Institutional white"); --Color for the mana bar.
	Custom_Color_6 = BRICKC("Institutional white"); --Color for the health bar.
	Custom_Color_7 = BRICKC("Institutional white"); --Color for the stun bar.

	Custom_Color_8 = BRICKC("Institutional white"); --Background for the mana bar.
	Custom_Color_9 = BRICKC("Institutional white"); --Background for the secondary mana bar.
	Custom_Color_10 = BRICKC("Institutional white"); --Background for the stun bar.
	Custom_Color_11 = BRICKC("Institutional white"); --Background for the health bar.
	Custom_Color_12 = BRICKC("Institutional white"); --Background for the abilities.
}


Player_Size = 1 --Size of the player.
Animation_Speed = 2
Animation_Speed2 = 1
Frame_Speed = 1 / 60 -- (1 / 30) OR (1 / 60)

local Speed = 28
Humanoid.JumpPower = 80
local Effects2 = {}

--//=================================\\
--|| 	  END OF CUSTOMIZATION
--\\=================================//

--//=================================\\
--|| 	      SOME FUNCTIONS
--\\=================================//

function Raycast(POSITION, DIRECTION, RANGE, IGNOREDECENDANTS)
	return workspace:FindPartOnRay(Ray.new(POSITION, DIRECTION.unit * RANGE), IGNOREDECENDANTS)
end

function PositiveAngle(NUMBER)
	if NUMBER >= 0 then
		NUMBER = 0
	end
	return NUMBER
end

function NegativeAngle(NUMBER)
	if NUMBER <= 0 then
		NUMBER = 0
	end
	return NUMBER
end

function Swait(NUMBER)
	if NUMBER == 0 or NUMBER == nil then
		ArtificialHB.Event:wait()
	else
		for i = 1, NUMBER do
			ArtificialHB.Event:wait()
		end
	end
end

function CreateMesh(MESH, PARENT, MESHTYPE, MESHID, TEXTUREID, SCALE, OFFSET)
	local NEWMESH = IT(MESH)
	if MESH == "SpecialMesh" then
		NEWMESH.MeshType = MESHTYPE
		if MESHID ~= "nil" and MESHID ~= "" then
			NEWMESH.MeshId = "http://www.roblox.com/asset/?id="..MESHID
		end
		if TEXTUREID ~= "nil" and TEXTUREID ~= "" then
			NEWMESH.TextureId = "http://www.roblox.com/asset/?id="..TEXTUREID
		end
	end
	NEWMESH.Offset = OFFSET or VT(0, 0, 0)
	NEWMESH.Scale = SCALE
	NEWMESH.Parent = PARENT
	return NEWMESH
end

function CreatePart(FORMFACTOR, PARENT, MATERIAL, REFLECTANCE, TRANSPARENCY, BRICKCOLOR, NAME, SIZE, ANCHOR)
	local NEWPART = IT("Part")
	NEWPART.formFactor = FORMFACTOR
	NEWPART.Reflectance = REFLECTANCE
	NEWPART.Transparency = TRANSPARENCY
	NEWPART.CanCollide = false
	NEWPART.Locked = true
	NEWPART.Anchored = true
	if ANCHOR == false then
		NEWPART.Anchored = false
	end
	NEWPART.BrickColor = BRICKC(tostring(BRICKCOLOR))
	NEWPART.Name = NAME
	NEWPART.Size = SIZE
	NEWPART.Position = Torso.Position
	NEWPART.Material = MATERIAL
	NEWPART:BreakJoints()
	NEWPART.Parent = PARENT
	return NEWPART
end

	local function weldBetween(a, b)
	    local weldd = Instance.new("ManualWeld")
	    weldd.Part0 = a
	    weldd.Part1 = b
	    weldd.C0 = CFrame.new()
	    weldd.C1 = b.CFrame:inverse() * a.CFrame
	    weldd.Parent = a
	    return weldd
	end


function QuaternionFromCFrame(cf)
	local mx, my, mz, m00, m01, m02, m10, m11, m12, m20, m21, m22 = cf:components()
	local trace = m00 + m11 + m22
	if trace > 0 then 
		local s = math.sqrt(1 + trace)
		local recip = 0.5 / s
		return (m21 - m12) * recip, (m02 - m20) * recip, (m10 - m01) * recip, s * 0.5
	else
		local i = 0
		if m11 > m00 then
			i = 1
		end
		if m22 > (i == 0 and m00 or m11) then
			i = 2
		end
		if i == 0 then
			local s = math.sqrt(m00 - m11 - m22 + 1)
			local recip = 0.5 / s
			return 0.5 * s, (m10 + m01) * recip, (m20 + m02) * recip, (m21 - m12) * recip
		elseif i == 1 then
			local s = math.sqrt(m11 - m22 - m00 + 1)
			local recip = 0.5 / s
			return (m01 + m10) * recip, 0.5 * s, (m21 + m12) * recip, (m02 - m20) * recip
		elseif i == 2 then
			local s = math.sqrt(m22 - m00 - m11 + 1)
			local recip = 0.5 / s return (m02 + m20) * recip, (m12 + m21) * recip, 0.5 * s, (m10 - m01) * recip
		end
	end
end
 
function QuaternionToCFrame(px, py, pz, x, y, z, w)
	local xs, ys, zs = x + x, y + y, z + z
	local wx, wy, wz = w * xs, w * ys, w * zs
	local xx = x * xs
	local xy = x * ys
	local xz = x * zs
	local yy = y * ys
	local yz = y * zs
	local zz = z * zs
	return CFrame.new(px, py, pz, 1 - (yy + zz), xy - wz, xz + wy, xy + wz, 1 - (xx + zz), yz - wx, xz - wy, yz + wx, 1 - (xx + yy))
end
 
function QuaternionSlerp(a, b, t)
	local cosTheta = a[1] * b[1] + a[2] * b[2] + a[3] * b[3] + a[4] * b[4]
	local startInterp, finishInterp;
	if cosTheta >= 0.0001 then
		if (1 - cosTheta) > 0.0001 then
			local theta = ACOS(cosTheta)
			local invSinTheta = 1 / SIN(theta)
			startInterp = SIN((1 - t) * theta) * invSinTheta
			finishInterp = SIN(t * theta) * invSinTheta
		else
			startInterp = 1 - t
			finishInterp = t
		end
	else
		if (1 + cosTheta) > 0.0001 then
			local theta = ACOS(-cosTheta)
			local invSinTheta = 1 / SIN(theta)
			startInterp = SIN((t - 1) * theta) * invSinTheta
			finishInterp = SIN(t * theta) * invSinTheta
		else
			startInterp = t - 1
			finishInterp = t
		end
	end
	return a[1] * startInterp + b[1] * finishInterp, a[2] * startInterp + b[2] * finishInterp, a[3] * startInterp + b[3] * finishInterp, a[4] * startInterp + b[4] * finishInterp
end

function Clerp(a, b, t)
	local qa = {QuaternionFromCFrame(a)}
	local qb = {QuaternionFromCFrame(b)}
	local ax, ay, az = a.x, a.y, a.z
	local bx, by, bz = b.x, b.y, b.z
	local _t = 1 - t
	return QuaternionToCFrame(_t * ax + t * bx, _t * ay + t * by, _t * az + t * bz, QuaternionSlerp(qa, qb, t))
end

function CreateFrame(PARENT, TRANSPARENCY, BORDERSIZEPIXEL, POSITION, SIZE, COLOR, BORDERCOLOR, NAME)
	local frame = IT("Frame")
	frame.BackgroundTransparency = TRANSPARENCY
	frame.BorderSizePixel = BORDERSIZEPIXEL
	frame.Position = POSITION
	frame.Size = SIZE
	frame.BackgroundColor3 = COLOR
	frame.BorderColor3 = BORDERCOLOR
	frame.Name = NAME
	frame.Parent = PARENT
	return frame
end

function CreateLabel(PARENT, TEXT, TEXTCOLOR, TEXTFONTSIZE, TEXTFONT, TRANSPARENCY, BORDERSIZEPIXEL, STROKETRANSPARENCY, NAME)
	local label = IT("TextLabel")
	label.BackgroundTransparency = 1
	label.Size = UD2(1, 0, 1, 0)
	label.Position = UD2(0, 0, 0, 0)
	label.TextColor3 = TEXTCOLOR
	label.TextStrokeTransparency = STROKETRANSPARENCY
	label.TextTransparency = TRANSPARENCY
	label.FontSize = TEXTFONTSIZE
	label.Font = TEXTFONT
	label.BorderSizePixel = BORDERSIZEPIXEL
	label.TextScaled = false
	label.Text = TEXT
	label.Name = NAME
	label.Parent = PARENT
	return label
end

function NoOutlines(PART)
	PART.TopSurface, PART.BottomSurface, PART.LeftSurface, PART.RightSurface, PART.FrontSurface, PART.BackSurface = 10, 10, 10, 10, 10, 10
end

function CreateWeldOrSnapOrMotor(TYPE, PARENT, PART0, PART1, C0, C1)
	local NEWWELD = IT(TYPE)
	NEWWELD.Part0 = PART0
	NEWWELD.Part1 = PART1
	NEWWELD.C0 = C0
	NEWWELD.C1 = C1
	NEWWELD.Parent = PARENT
	return NEWWELD
end

local S = IT("Sound")
function CreateSound(ID, PARENT, VOLUME, PITCH, DOESLOOP)
	local NEWSOUND = nil
	coroutine.resume(coroutine.create(function()
		NEWSOUND = S:Clone()
		NEWSOUND.Parent = PARENT
		NEWSOUND.Volume = VOLUME
		NEWSOUND.Pitch = PITCH
		NEWSOUND.SoundId = "http://www.roblox.com/asset/?id="..ID
		NEWSOUND:play()
		if DOESLOOP == true then
			NEWSOUND.Looped = true
		else
			repeat wait(1) until NEWSOUND.Playing == false or NEWSOUND.Parent ~= PARENT
			NEWSOUND:remove()
		end
	end))
	return NEWSOUND
end

function CFrameFromTopBack(at, top, back)
	local right = top:Cross(back)
	return CF(at.x, at.y, at.z, right.x, top.x, back.x, right.y, top.y, back.y, right.z, top.z, back.z)
end

--WACKYEFFECT({EffectType = "", Size = VT(1,1,1), Size2 = VT(0,0,0), Transparency = 0, Transparency2 = 1, CFrame = CF(), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1,1,1), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
function WACKYEFFECT(Table)
	local TYPE = (Table.EffectType or "Sphere")
	local SIZE = (Table.Size or VT(1,1,1))
	local ENDSIZE = (Table.Size2 or VT(0,0,0))
	local TRANSPARENCY = (Table.Transparency or 0)
	local ENDTRANSPARENCY = (Table.Transparency2 or 1)
	local CFRAME = (Table.CFrame or Torso.CFrame)
	local MOVEDIRECTION = (Table.MoveToPos or nil)
	local ROTATION1 = (Table.RotationX or 0)
	local ROTATION2 = (Table.RotationY or 0)
	local ROTATION3 = (Table.RotationZ or 0)
	local MATERIAL = (Table.Material or "Neon")
	local COLOR = (Table.Color or C3(1,1,1))
	local TIME = (Table.Time or 45)
	local SOUNDID = (Table.SoundID or nil)
	local SOUNDPITCH = (Table.SoundPitch or nil)
	local SOUNDVOLUME = (Table.SoundVolume or nil)
	coroutine.resume(coroutine.create(function()
		local PLAYSSOUND = false
		local SOUND = nil
		local EFFECT = CreatePart(3, Effects, MATERIAL, 0, TRANSPARENCY, BRICKC("Pearl"), "Effect", VT(1,1,1), true)
		if SOUNDID ~= nil and SOUNDPITCH ~= nil and SOUNDVOLUME ~= nil then
			PLAYSSOUND = true
			SOUND = CreateSound(SOUNDID, EFFECT, SOUNDVOLUME, SOUNDPITCH, false)
		end
		EFFECT.Color = COLOR
		local MSH = nil
		if TYPE == "Sphere" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "Sphere", "", "", SIZE, VT(0,0,0))
		elseif TYPE == "Block" then
			MSH = IT("BlockMesh",EFFECT)
			MSH.Scale = VT(SIZE.X,SIZE.X,SIZE.X)
		elseif TYPE == "Wave" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "20329976", "", SIZE, VT(0,0,-SIZE.X/8))
		elseif TYPE == "Ring" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "559831844", "", VT(SIZE.X,SIZE.X,0.1), VT(0,0,0))
		elseif TYPE == "Slash" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "662586858", "", VT(SIZE.X/10,0,SIZE.X/10), VT(0,0,0))
		elseif TYPE == "Round Slash" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "662585058", "", VT(SIZE.X/10,0,SIZE.X/10), VT(0,0,0))
		elseif TYPE == "Swirl" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "1051557", "", SIZE, VT(0,0,0))
		elseif TYPE == "Skull" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "4770583", "", SIZE, VT(0,0,0))
		elseif TYPE == "Crystal" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "9756362", "", SIZE, VT(0,0,0))
		end
		if MSH ~= nil then
			local MOVESPEED = nil
			if MOVEDIRECTION ~= nil then
				MOVESPEED = (CFRAME.p - MOVEDIRECTION).Magnitude/TIME
			end
			local GROWTH = SIZE - ENDSIZE
			local TRANS = TRANSPARENCY - ENDTRANSPARENCY
			if TYPE == "Block" then
				EFFECT.CFrame = CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))
			else
				EFFECT.CFrame = CFRAME
			end
			for LOOP = 1, TIME+1 do
				Swait()
				MSH.Scale = MSH.Scale - GROWTH/TIME
				if TYPE == "Wave" then
					MSH.Offset = VT(0,0,-MSH.Scale.X/8)
				end
				EFFECT.Transparency = EFFECT.Transparency - TRANS/TIME
				if TYPE == "Block" then
					EFFECT.CFrame = CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))
				else
					EFFECT.CFrame = EFFECT.CFrame*ANGLES(RAD(ROTATION1),RAD(ROTATION2),RAD(ROTATION3))
				end
				if MOVEDIRECTION ~= nil then
					local ORI = EFFECT.Orientation
					EFFECT.CFrame = CF(EFFECT.Position,MOVEDIRECTION)*CF(0,0,-MOVESPEED)
					EFFECT.Orientation = ORI
				end
			end
			if PLAYSSOUND == false then
				EFFECT:remove()
			else
				SOUND.Stopped:Connect(function()
					EFFECT:remove()
				end)
			end
		else
			if PLAYSSOUND == false then
				EFFECT:remove()
			else
				repeat Swait() until SOUND.Playing == false
				EFFECT:remove()
			end
		end
	end))
end
for _, c in pairs(Character:GetChildren()) do
	if c.ClassName == "Part" then
		table.insert(BODY,{c,Character})
		for _, q in pairs(c:GetChildren()) do
			if q.ClassName == "Motor6D" or q.ClassName == "Weld" or q.ClassName == "ManualWeld" then
				table.insert(BODY,{q,c})
			end
		end
	end
end

function refit()
	Character.Parent = workspace
	for e = 1, #BODY do
		if BODY[e] ~= nil then
			local STUFF = BODY[e]
			local PART = STUFF[1]
			local PARENT = STUFF[2]
			PART.Parent = PARENT
		end
	end
end

function MakeForm(PART,TYPE)
	if TYPE == "Cyl" then
		local MSH = IT("CylinderMesh",PART)
	elseif TYPE == "Ball" then
		local MSH = IT("SpecialMesh",PART)
		MSH.MeshType = "Sphere"
	elseif TYPE == "Wedge" then
		local MSH = IT("SpecialMesh",PART)
		MSH.MeshType = "Wedge"
	end
end

function SpawnTrail(FROM,TO,BIG)
	local TRAIL = CreatePart(3, Effects, "Neon", 0, 0.5, "Lime green", "Trail", VT(0,0,0))
	MakeForm(TRAIL,"Cyl")
	local DIST = (FROM - TO).Magnitude
	if BIG == true then
		TRAIL.Size = VT(0.5,DIST,0.5)
	else
		TRAIL.Size = VT(0.25,DIST,0.25)
	end
	TRAIL.CFrame = CF(FROM, TO) * CF(0, 0, -DIST/2) * ANGLES(RAD(90),RAD(0),RAD(0))
	coroutine.resume(coroutine.create(function()
		for i = 1, 5 do
			Swait()
			TRAIL.Transparency = TRAIL.Transparency + 0.1
		end
		TRAIL:remove()
	end))
end

Debris = game:GetService("Debris")

function CastProperRay(StartPos, EndPos, Distance, Ignore)
	local DIRECTION = CF(StartPos,EndPos).lookVector
	return Raycast(StartPos, DIRECTION, Distance, Ignore)
end

function turnto(position)
	RootPart.CFrame=CFrame.new(RootPart.CFrame.p,VT(position.X,RootPart.Position.Y,position.Z)) * CFrame.new(0, 0, 0)
end
local asd = Instance.new("ParticleEmitter")
asd.Color = ColorSequence.new(Color3.new(0.5, 0, 0), Color3.new(.3, 0, 0))
asd.LightEmission = .1
asd.Texture = "http://www.roblox.com/asset/?ID=291880914"
aaa = NumberSequence.new({NumberSequenceKeypoint.new(0, 0.6),NumberSequenceKeypoint.new(1, 2)})
bbb = NumberSequence.new({NumberSequenceKeypoint.new(0, 1),NumberSequenceKeypoint.new(0.0636, 0), NumberSequenceKeypoint.new(1, 1)})
asd.Transparency = bbb
asd.Size = aaa
asd.ZOffset = .9
asd.Acceleration = Vector3.new(0, -15, 0)
asd.LockedToPart = false
asd.EmissionDirection = "Back"
asd.Lifetime = NumberRange.new(1, 2)
asd.Rotation = NumberRange.new(-100, 100)
asd.RotSpeed = NumberRange.new(-100, 100)
asd.Speed = NumberRange.new(10)
asd.Enabled = false
asd.VelocitySpread = 999

function getbloody(victim,amount)
	local PART = CreatePart(3, Effects, "Metal", 0, 1, "Mid gray", "Blood", victim.Size)
	PART.CFrame = victim.CFrame
	local HITPLAYERSOUNDS = {"356551938","264486467"}
	Debris:AddItem(PART,5)
	CreateSound(HITPLAYERSOUNDS[MRANDOM(1, #HITPLAYERSOUNDS)], PART, 1, (math.random(8,12)/10))
	CreateSound(HITPLAYERSOUNDS[MRANDOM(1, #HITPLAYERSOUNDS)], PART, 1, (math.random(8,12)/10))
	CreateSound(HITPLAYERSOUNDS[MRANDOM(1, #HITPLAYERSOUNDS)], PART, 1, (math.random(8,12)/10))
	local prtcl = asd:Clone()
	prtcl.Parent = PART
	prtcl:Emit(amount*10)
end

local Particle = IT("ParticleEmitter",nil)
Particle.Enabled = false
Particle.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0,1),NumberSequenceKeypoint.new(0.3,0.95),NumberSequenceKeypoint.new(1,1)})
Particle.LightEmission = 0.5
Particle.Rate = 150
Particle.ZOffset = 1
Particle.Rotation = NumberRange.new(-180, 180)
Particle.RotSpeed = NumberRange.new(-180, 180)
Particle.Texture = "http://www.roblox.com/asset/?id=304437537"
Particle.Color = ColorSequence.new(C3(0,0,0),C3(0.4,0,0))

--ParticleEmitter({Speed = 5, Drag = 0, Size1 = 1, Size2 = 5, Lifetime1 = 1, Lifetime2 = 1.5, Parent = Torso, Emit = 100, Offset = 360, Enabled = false})
function ParticleEmitter(Table)
	local PRTCL = Particle:Clone()
	local Speed = Table.Speed or 5
	local Drag = Table.Drag or 0
	local Size1 = Table.Size1 or 1
	local Size2 = Table.Size2 or 5
	local Lifetime1 = Table.Lifetime1 or 1
	local Lifetime2 = Table.Lifetime2 or 1.5
	local Parent = Table.Parent or Torso
	local Emit = Table.Emit or 100
	local Offset = Table.Offset or 360
	local Acel = Table.Acel or VT(0,0,0)
	local Enabled = Table.Enabled or false
	PRTCL.Parent = Parent
	PRTCL.Size = NumberSequence.new(Size1,Size2)
	PRTCL.Lifetime = NumberRange.new(Lifetime1,Lifetime2)
	PRTCL.Speed = NumberRange.new(Speed)
	PRTCL.VelocitySpread = Offset
	PRTCL.Drag = Drag
	PRTCL.Acceleration = Acel
	if Enabled == false then
		PRTCL:Emit(Emit)
		Debris:AddItem(PRTCL,Lifetime2)
	else
		PRTCL.Enabled = true
	end
	return PRTCL
end

function Raycast(POSITION, DIRECTION, RANGE, IGNOREDECENDANTS)
	return workspace:FindPartOnRay(Ray.new(POSITION, DIRECTION.unit * RANGE), IGNOREDECENDANTS)
end

function PositiveAngle(NUMBER)
	if NUMBER >= 0 then
		NUMBER = 0
	end
	return NUMBER
end

function NegativeAngle(NUMBER)
	if NUMBER <= 0 then
		NUMBER = 0
	end
	return NUMBER
end

function Swait(NUMBER)
	if NUMBER == 0 or NUMBER == nil then
		ArtificialHB.Event:wait()
	else
		for i = 1, NUMBER do
			ArtificialHB.Event:wait()
		end
	end
end

function CreateMesh(MESH, PARENT, MESHTYPE, MESHID, TEXTUREID, SCALE, OFFSET)
	local NEWMESH = IT(MESH)
	if MESH == "SpecialMesh" then
		NEWMESH.MeshType = MESHTYPE
		if MESHID ~= "nil" and MESHID ~= "" then
			NEWMESH.MeshId = "http://www.roblox.com/asset/?id="..MESHID
		end
		if TEXTUREID ~= "nil" and TEXTUREID ~= "" then
			NEWMESH.TextureId = "http://www.roblox.com/asset/?id="..TEXTUREID
		end
	end
	NEWMESH.Offset = OFFSET or VT(0, 0, 0)
	NEWMESH.Scale = SCALE
	NEWMESH.Parent = PARENT
	return NEWMESH
end

function CreatePart(FORMFACTOR, PARENT, MATERIAL, REFLECTANCE, TRANSPARENCY, BRICKCOLOR, NAME, SIZE, ANCHOR)
	local NEWPART = IT("Part")
	NEWPART.formFactor = FORMFACTOR
	NEWPART.Reflectance = REFLECTANCE
	NEWPART.Transparency = TRANSPARENCY
	NEWPART.CanCollide = false
	NEWPART.Locked = true
	NEWPART.Anchored = true
	if ANCHOR == false then
		NEWPART.Anchored = false
	end
	NEWPART.BrickColor = BRICKC(tostring(BRICKCOLOR))
	NEWPART.Name = NAME
	NEWPART.Size = SIZE
	NEWPART.Position = Torso.Position
	NEWPART.Material = MATERIAL
	NEWPART:BreakJoints()
	NEWPART.Parent = PARENT
	return NEWPART
end

	local function weldBetween(a, b)
	    local weldd = Instance.new("ManualWeld")
	    weldd.Part0 = a
	    weldd.Part1 = b
	    weldd.C0 = CFrame.new()
	    weldd.C1 = b.CFrame:inverse() * a.CFrame
	    weldd.Parent = a
	    return weldd
	end


function QuaternionFromCFrame(cf)
	local mx, my, mz, m00, m01, m02, m10, m11, m12, m20, m21, m22 = cf:components()
	local trace = m00 + m11 + m22
	if trace > 0 then 
		local s = math.sqrt(1 + trace)
		local recip = 0.5 / s
		return (m21 - m12) * recip, (m02 - m20) * recip, (m10 - m01) * recip, s * 0.5
	else
		local i = 0
		if m11 > m00 then
			i = 1
		end
		if m22 > (i == 0 and m00 or m11) then
			i = 2
		end
		if i == 0 then
			local s = math.sqrt(m00 - m11 - m22 + 1)
			local recip = 0.5 / s
			return 0.5 * s, (m10 + m01) * recip, (m20 + m02) * recip, (m21 - m12) * recip
		elseif i == 1 then
			local s = math.sqrt(m11 - m22 - m00 + 1)
			local recip = 0.5 / s
			return (m01 + m10) * recip, 0.5 * s, (m21 + m12) * recip, (m02 - m20) * recip
		elseif i == 2 then
			local s = math.sqrt(m22 - m00 - m11 + 1)
			local recip = 0.5 / s return (m02 + m20) * recip, (m12 + m21) * recip, 0.5 * s, (m10 - m01) * recip
		end
	end
end
 
function QuaternionToCFrame(px, py, pz, x, y, z, w)
	local xs, ys, zs = x + x, y + y, z + z
	local wx, wy, wz = w * xs, w * ys, w * zs
	local xx = x * xs
	local xy = x * ys
	local xz = x * zs
	local yy = y * ys
	local yz = y * zs
	local zz = z * zs
	return CFrame.new(px, py, pz, 1 - (yy + zz), xy - wz, xz + wy, xy + wz, 1 - (xx + zz), yz - wx, xz - wy, yz + wx, 1 - (xx + yy))
end
 
function QuaternionSlerp(a, b, t)
	local cosTheta = a[1] * b[1] + a[2] * b[2] + a[3] * b[3] + a[4] * b[4]
	local startInterp, finishInterp;
	if cosTheta >= 0.0001 then
		if (1 - cosTheta) > 0.0001 then
			local theta = ACOS(cosTheta)
			local invSinTheta = 1 / SIN(theta)
			startInterp = SIN((1 - t) * theta) * invSinTheta
			finishInterp = SIN(t * theta) * invSinTheta
		else
			startInterp = 1 - t
			finishInterp = t
		end
	else
		if (1 + cosTheta) > 0.0001 then
			local theta = ACOS(-cosTheta)
			local invSinTheta = 1 / SIN(theta)
			startInterp = SIN((t - 1) * theta) * invSinTheta
			finishInterp = SIN(t * theta) * invSinTheta
		else
			startInterp = t - 1
			finishInterp = t
		end
	end
	return a[1] * startInterp + b[1] * finishInterp, a[2] * startInterp + b[2] * finishInterp, a[3] * startInterp + b[3] * finishInterp, a[4] * startInterp + b[4] * finishInterp
end

function Clerp(a, b, t)
	local qa = {QuaternionFromCFrame(a)}
	local qb = {QuaternionFromCFrame(b)}
	local ax, ay, az = a.x, a.y, a.z
	local bx, by, bz = b.x, b.y, b.z
	local _t = 1 - t
	return QuaternionToCFrame(_t * ax + t * bx, _t * ay + t * by, _t * az + t * bz, QuaternionSlerp(qa, qb, t))
end

function CreateFrame(PARENT, TRANSPARENCY, BORDERSIZEPIXEL, POSITION, SIZE, COLOR, BORDERCOLOR, NAME)
	local frame = IT("Frame")
	frame.BackgroundTransparency = TRANSPARENCY
	frame.BorderSizePixel = BORDERSIZEPIXEL
	frame.Position = POSITION
	frame.Size = SIZE
	frame.BackgroundColor3 = COLOR
	frame.BorderColor3 = BORDERCOLOR
	frame.Name = NAME
	frame.Parent = PARENT
	return frame
end

function CreateLabel(PARENT, TEXT, TEXTCOLOR, TEXTFONTSIZE, TEXTFONT, TRANSPARENCY, BORDERSIZEPIXEL, STROKETRANSPARENCY, NAME)
	local label = IT("TextLabel")
	label.BackgroundTransparency = 1
	label.Size = UD2(1, 0, 1, 0)
	label.Position = UD2(0, 0, 0, 0)
	label.TextColor3 = TEXTCOLOR
	label.TextStrokeTransparency = STROKETRANSPARENCY
	label.TextTransparency = TRANSPARENCY
	label.FontSize = TEXTFONTSIZE
	label.Font = TEXTFONT
	label.BorderSizePixel = BORDERSIZEPIXEL
	label.TextScaled = false
	label.Text = TEXT
	label.Name = NAME
	label.Parent = PARENT
	return label
end

function NoOutlines(PART)
	PART.TopSurface, PART.BottomSurface, PART.LeftSurface, PART.RightSurface, PART.FrontSurface, PART.BackSurface = 10, 10, 10, 10, 10, 10
end

function CreateWeldOrSnapOrMotor(TYPE, PARENT, PART0, PART1, C0, C1)
	local NEWWELD = IT(TYPE)
	NEWWELD.Part0 = PART0
	NEWWELD.Part1 = PART1
	NEWWELD.C0 = C0
	NEWWELD.C1 = C1
	NEWWELD.Parent = PARENT
	return NEWWELD
end

local S = IT("Sound")
function CreateSound(ID, PARENT, VOLUME, PITCH, DOESLOOP)
	local NEWSOUND = nil
	coroutine.resume(coroutine.create(function()
		NEWSOUND = S:Clone()
		NEWSOUND.Parent = PARENT
		NEWSOUND.Volume = VOLUME
		NEWSOUND.Pitch = PITCH
		NEWSOUND.SoundId = "rbxassetid://"..ID
		NEWSOUND:play()
		if DOESLOOP == true then
			NEWSOUND.Looped = true
		else
			repeat wait(1) until NEWSOUND.Playing == false
			NEWSOUND:remove()
		end
	end))
	return NEWSOUND
end

function CFrameFromTopBack(at, top, back)
	local right = top:Cross(back)
	return CF(at.x, at.y, at.z, right.x, top.x, back.x, right.y, top.y, back.y, right.z, top.z, back.z)
end

function R_RANDOM(CFRAME,DIST)
	return CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))*CF(0,0,-DIST)
end

--WACKYEFFECT({EffectType = "", Size = VT(1,1,1), Size2 = VT(0,0,0), Transparency = 0, Transparency2 = 1, CFrame = CF(), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1,1,1), SoundID = nil, SoundPitch = nil, SoundVolume = nil, UseBoomerangMath = false, Boomerang = 0, SizeBoomerang = 0})
function WACKYEFFECT(Table)
	local TYPE = (Table.EffectType or "Sphere")
	local SIZE = (Table.Size or VT(1,1,1))
	local ENDSIZE = (Table.Size2 or VT(0,0,0))
	local TRANSPARENCY = (Table.Transparency or 0)
	local ENDTRANSPARENCY = (Table.Transparency2 or 1)
	local CFRAME = (Table.CFrame or Torso.CFrame)
	local MOVEDIRECTION = (Table.MoveToPos or nil)
	local ROTATION1 = (Table.RotationX or 0)
	local ROTATION2 = (Table.RotationY or 0)
	local ROTATION3 = (Table.RotationZ or 0)
	local MATERIAL = (Table.Material or "Neon")
	local COLOR = (Table.Color or C3(1,1,1))
	local TIME = (Table.Time or 45)
	local SOUNDID = (Table.SoundID or nil)
	local SOUNDPITCH = (Table.SoundPitch or nil)
	local SOUNDVOLUME = (Table.SoundVolume or nil)
	local USEBOOMERANGMATH = (Table.UseBoomerangMath or false)
	local BOOMERANG = (Table.Boomerang or 0)
	local SIZEBOOMERANG = (Table.SizeBoomerang or 0)
	coroutine.resume(coroutine.create(function()
		local PLAYSSOUND = false
		local SOUND = nil
		local EFFECT = CreatePart(3, Effects, MATERIAL, 0, TRANSPARENCY, BRICKC("Pearl"), "Effect", VT(1,1,1), true)
		if SOUNDID ~= nil and SOUNDPITCH ~= nil and SOUNDVOLUME ~= nil then
			PLAYSSOUND = true
			SOUND = CreateSound(SOUNDID, EFFECT, SOUNDVOLUME, SOUNDPITCH, false)
		end
		EFFECT.Color = COLOR
		local MSH = nil
		if TYPE == "Sphere" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "Sphere", "", "", SIZE, VT(0,0,0))
		elseif TYPE == "Block" or TYPE == "Box" then
			MSH = IT("BlockMesh",EFFECT)
			MSH.Scale = SIZE
		elseif TYPE == "Wave" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "20329976", "", SIZE, VT(0,0,-SIZE.X/8))
		elseif TYPE == "Ring" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "559831844", "", VT(SIZE.X,SIZE.X,0.1), VT(0,0,0))
		elseif TYPE == "Slash" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "662586858", "", VT(SIZE.X/10,0,SIZE.X/10), VT(0,0,0))
		elseif TYPE == "Round Slash" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "662585058", "", VT(SIZE.X/10,0,SIZE.X/10), VT(0,0,0))
		elseif TYPE == "Swirl" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "168892432", "", SIZE, VT(0,0,0))
		elseif TYPE == "Skull" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "4770583", "", SIZE, VT(0,0,0))
		elseif TYPE == "Crystal" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "9756362", "", SIZE, VT(0,0,0))
		end
		if MSH ~= nil then
			local BOOMR1 = 1+BOOMERANG/50
			local BOOMR2 = 1+SIZEBOOMERANG/50
			local MOVESPEED = nil
			if MOVEDIRECTION ~= nil then
				if USEBOOMERANGMATH == true then
					MOVESPEED = ((CFRAME.p - MOVEDIRECTION).Magnitude/TIME)*BOOMR1
				else
					MOVESPEED = ((CFRAME.p - MOVEDIRECTION).Magnitude/TIME)
				end
			end
			local GROWTH = nil
			if USEBOOMERANGMATH == true then
				GROWTH = (SIZE - ENDSIZE)*(BOOMR2+1)
			else
				GROWTH = (SIZE - ENDSIZE)
			end
			local TRANS = TRANSPARENCY - ENDTRANSPARENCY
			if TYPE == "Block" then
				EFFECT.CFrame = CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))
			else
				EFFECT.CFrame = CFRAME
			end
			if USEBOOMERANGMATH == true then
				for LOOP = 1, TIME+1 do
					Swait()
					MSH.Scale = MSH.Scale - (VT((GROWTH.X)*((1 - (LOOP/TIME)*BOOMR2)),(GROWTH.Y)*((1 - (LOOP/TIME)*BOOMR2)),(GROWTH.Z)*((1 - (LOOP/TIME)*BOOMR2)))*BOOMR2)/TIME
					if TYPE == "Wave" then
						MSH.Offset = VT(0,0,-MSH.Scale.Z/8)
					end
					EFFECT.Transparency = EFFECT.Transparency - TRANS/TIME
					if TYPE == "Block" then
						EFFECT.CFrame = CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))
					else
						EFFECT.CFrame = EFFECT.CFrame*ANGLES(RAD(ROTATION1),RAD(ROTATION2),RAD(ROTATION3))
					end
					if MOVEDIRECTION ~= nil then
						local ORI = EFFECT.Orientation
						EFFECT.CFrame = CF(EFFECT.Position,MOVEDIRECTION)*CF(0,0,-(MOVESPEED)*((1 - (LOOP/TIME)*BOOMR1)))
						EFFECT.CFrame = CF(EFFECT.Position)*ANGLES(RAD(ORI.X),RAD(ORI.Y),RAD(ORI.Z))
					end
				end
			else
				for LOOP = 1, TIME+1 do
					Swait()
					MSH.Scale = MSH.Scale - GROWTH/TIME
					if TYPE == "Wave" then
						MSH.Offset = VT(0,0,-MSH.Scale.Z/8)
					end
					EFFECT.Transparency = EFFECT.Transparency - TRANS/TIME
					if TYPE == "Block" then
						EFFECT.CFrame = CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))
					else
						EFFECT.CFrame = EFFECT.CFrame*ANGLES(RAD(ROTATION1),RAD(ROTATION2),RAD(ROTATION3))
					end
					if MOVEDIRECTION ~= nil then
						local ORI = EFFECT.Orientation
						EFFECT.CFrame = CF(EFFECT.Position,MOVEDIRECTION)*CF(0,0,-MOVESPEED)
						EFFECT.CFrame = CF(EFFECT.Position)*ANGLES(RAD(ORI.X),RAD(ORI.Y),RAD(ORI.Z))
					end
				end
			end
			EFFECT.Transparency = 1
			if PLAYSSOUND == false then
				EFFECT:remove()
			else
				repeat Swait() until EFFECT:FindFirstChildOfClass("Sound") == nil
				EFFECT:remove()
			end
		else
			if PLAYSSOUND == false then
				EFFECT:remove()
			else
				repeat Swait() until EFFECT:FindFirstChildOfClass("Sound") == nil
				EFFECT:remove()
			end
		end
	end))
end


local HATWELD = nil
function Pose(WhichPose,Speed,Time,Magic,Gyro,Tors)
	PLAYMAINANIM = false
	if WhichPose == "Cast1" then
		for i=0, Time, 0.1 / Animation_Speed do
			Swait()
			if Magic == true then
				WACKYEFFECT({Time = 15, EffectType = "Crystal", Size = VT(1,1,1)*1.5, Size2 = VT(0,4,0), Transparency = 0, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1,0)*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360))), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Glass", Color = BRICKC"Alder".Color, SoundID = nil, SoundPitch = nil, SoundVolume = nil})
			end
			if Gyro ~= nil and Gyro ~= false then
				Gyro.cframe = CF(RootPart.Position,Mouse.Hit.p)
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(35)), Speed / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5 - 6.5 * SIN(SINE / 12)), RAD(0), RAD(-35)), Speed / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.65 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), -0.5) * ANGLES(RAD(125), RAD(0), RAD(45)) * RIGHTSHOULDERC0, Speed / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.5 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), 0.5) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, Speed / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.5, -0.5) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(-15)), Speed / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), Speed / Animation_Speed)
		end
	elseif WhichPose == "Cast2" then
		for i=0, Time, 0.1 / Animation_Speed do
			Swait()
			if Magic == true then
				WACKYEFFECT({Time = 15, EffectType = "Crystal", Size = VT(1,1,1)*1.5, Size2 = VT(0,4,0), Transparency = 0, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1,0)*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360))), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Glass", Color = BRICKC"Alder".Color, SoundID = nil, SoundPitch = nil, SoundVolume = nil})
				WACKYEFFECT({Time = 15, EffectType = "Crystal", Size = VT(1,1,1)*1.5, Size2 = VT(0,4,0), Transparency = 0, Transparency2 = 1, CFrame = LeftArm.CFrame*CF(0,-1,0)*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360))), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Glass", Color = BRICKC"Alder".Color, SoundID = nil, SoundPitch = nil, SoundVolume = nil})
			end
			if Gyro ~= nil and Gyro ~= false then
				Gyro.cframe = CF(RootPart.Position,Mouse.Hit.p)
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), Speed / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(-15 - 6.5 * SIN(SINE / 12)), RAD(0), RAD(0)), Speed / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.65 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), -0.5) * ANGLES(RAD(120), RAD(0), RAD(15)) * RIGHTSHOULDERC0, Speed / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.65 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), -0.5) * ANGLES(RAD(120), RAD(0), RAD(-15)) * LEFTSHOULDERC0, Speed / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.5, -0.5) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(-15)), Speed / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), Speed / Animation_Speed)
		end
	elseif WhichPose == "RightArmUp" then
		for i=0, Time, 0.1 / Animation_Speed do
			Swait()
			if Magic == true then
				WACKYEFFECT({Time = 15, EffectType = "Crystal", Size = VT(1,1,1)*1.5, Size2 = VT(0,4,0), Transparency = 0, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1,0)*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360))), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Glass", Color = BRICKC"Alder".Color, SoundID = nil, SoundPitch = nil, SoundVolume = nil})
			end
			if Gyro ~= nil and Gyro ~= false then
				Gyro.cframe = CF(RootPart.Position,Mouse.Hit.p)
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(15)), Speed / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(-5 - 6.5 * SIN(SINE / 12)), RAD(0), RAD(-15)), Speed / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.65 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), -0.2) * ANGLES(RAD(125), RAD(0), RAD(25)) * RIGHTSHOULDERC0, Speed / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.5 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), 0.5) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, Speed / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.5, -0.5) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(-15)), Speed / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), Speed / Animation_Speed)
		end
	elseif WhichPose == "Taunt" then
		for i=0, Time, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), Speed / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(-5 - 6.5 * SIN(SINE / 12)), RAD(0), RAD(0)), Speed / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.5 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), -0.5) * ANGLES(RAD(175), RAD(0), RAD(-35)) * RIGHTSHOULDERC0, Speed / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.5 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), 0.5) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, Speed / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.5, -0.5) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(-15)), Speed / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), Speed / Animation_Speed)
		end
		HATWELD.Part0 = RightArm
		HATWELD.C0 = CF(0,-1.1,-0.4) * ANGLES(RAD(0), RAD(0), RAD(180)) * ANGLES(RAD(-20), RAD(0), RAD(0))
		CreateSound(221057812,Torso,10,1,false)
		for i=0, Time*2, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), Speed / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(35 - 6.5 * SIN(SINE / 12)), RAD(0), RAD(0)), Speed / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.5 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), 0.5) * ANGLES(RAD(145), RAD(0), RAD(45)) * RIGHTSHOULDERC0, Speed / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.5 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), 0.5) * ANGLES(RAD(5), RAD(25), RAD(-15)) * LEFTSHOULDERC0, Speed / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.5, -0.5) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(-15)), Speed / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), Speed / Animation_Speed)
		end
		for i=0, Time, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), Speed / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5), RAD(0), RAD(0)), Speed / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35, 0.65, -0.5) * ANGLES(RAD(175), RAD(0), RAD(-35)) * RIGHTSHOULDERC0, Speed / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.5 + 0.15 * COS(SINE / 12) - 0.05 * COS(SINE / 12), 0.5) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, Speed / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.5, -0.5) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(-15)), Speed / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), Speed / Animation_Speed)
			HATWELD.C0 = Clerp(HATWELD.C0, CF(-0.5,-1.2,-0.3) * ANGLES(RAD(0), RAD(0), RAD(180)) * ANGLES(RAD(35), RAD(0), RAD(35)), Speed / Animation_Speed)
		end
		HATWELD.Part0 = Head
		HATWELD.C0 = CF(0,0.35,0)
	elseif WhichPose == "Prepare key" then
		for i=0, Time, 0.1 / Animation_Speed do
			Swait()
			if Gyro ~= nil and Gyro ~= false then
				Gyro.cframe = CF(RootPart.Position,Tors.Position)
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0*SIZE, 0*SIZE, 0*SIZE + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-45)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(45)), 0.15 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35*SIZE, 0.65*SIZE, 0.2*SIZE) * ANGLES(RAD(60), RAD(0), RAD(-25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-35), RAD(25 - 2.5 * SIN(SINE / 12)), RAD(55 - 2.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(-75), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
	elseif WhichPose == "Turn key" then
		for i=0, Time, 0.1 / Animation_Speed do
			Swait()
			if Gyro ~= nil and Gyro ~= false then
				Gyro.cframe = CF(RootPart.Position,Tors.Position)
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0*SIZE, 0*SIZE, 0*SIZE + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(45)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-45)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35*SIZE, 0.65*SIZE, -0.2*SIZE) * ANGLES(RAD(90), RAD(0), RAD(45)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-35), RAD(25 - 2.5 * SIN(SINE / 12)), RAD(55 - 2.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(75), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
	end
	PLAYMAINANIM = true
end


function MakeForm(PART,TYPE)
	if TYPE == "Cyl" then
		local MSH = IT("CylinderMesh",PART)
	elseif TYPE == "Ball" then
		local MSH = IT("SpecialMesh",PART)
		MSH.MeshType = "Sphere"
	elseif TYPE == "Wedge" then
		local MSH = IT("SpecialMesh",PART)
		MSH.MeshType = "Wedge"
	end
end

Debris = game:GetService("Debris")

function CastProperRay(StartPos, EndPos, Distance, Ignore)
	local DIRECTION = CF(StartPos,EndPos).lookVector
	local Ignore = ((type(Ignore) == "table" and Ignore) or {Ignore})
	return game:GetService("Workspace"):FindPartOnRayWithIgnoreList(Ray.new(StartPos, DIRECTION * Distance), Ignore)
end

local DECAL = IT("Decal")
function MagicRing()
	local RING = CreatePart(3, Effects, "Granite", 0, 1, "Maroon", "MagicRing", VT(0,0,0),true)
	local MESH = IT("BlockMesh",RING)
	local BOTTOMTEXTURE = DECAL:Clone()
	BOTTOMTEXTURE.Parent = RING
	BOTTOMTEXTURE.Face = "Bottom"
	BOTTOMTEXTURE.Name = "BottomTexture"
	local TOPTEXTURE = DECAL:Clone()
	TOPTEXTURE.Parent = RING
	TOPTEXTURE.Face = "Top"
	TOPTEXTURE.Name = "TopTexture"
	BOTTOMTEXTURE.Texture = "http://www.roblox.com/asset/?id=1208118228"
	TOPTEXTURE.Texture = "http://www.roblox.com/asset/?id=1208118228"
	BOTTOMTEXTURE.Color3 = C3(0,0,0)
	TOPTEXTURE.Color3 = C3(0,0,0)
	return RING,MESH,TOPTEXTURE,BOTTOMTEXTURE
end

function CharacterFade(COLOR,TIMER,MOVEDIRECTION,PARENT)
	coroutine.resume(coroutine.create(function()
		local FADE = IT("Model",Effects)
		if PARENT ~= nil then
			FADE.Parent = PARENT
		end
		FADE.Name = "FadingEffect"
		for _, c in pairs(Character:GetChildren()) do
			if c.ClassName == "Part" then
				c.CanCollide = false
				local FADER = CreatePart(3, FADE, "Neon", 0, 0.75, BRICKC("Pearl"), c.Name, c.Size, true)
				FADER.CFrame = c.CFrame
				FADER.Color = COLOR
				if FADER.Name == "Head" then
					Head:FindFirstChildOfClass("SpecialMesh"):Clone().Parent = FADER
				elseif FADER.Name == "HumanoidRootPart" then
					FADE.PrimaryPart = FADER
					FADER.Transparency = 1
				end
			end
		end
		local TRANS = 0.25/TIMER
		local DIST = nil
		if MOVEDIRECTION ~= nil then
			DIST = (FADE.PrimaryPart.Position - MOVEDIRECTION).Magnitude
		end
		for i = 1, TIMER do
			Swait()
			for _, c in pairs(FADE:GetChildren()) do
				if c.ClassName == "Part" then
					c.Transparency = c.Transparency + TRANS
				end
			end
			if MOVEDIRECTION ~= nil then
				local ORI = FADE.PrimaryPart.Orientation
				FADE:SetPrimaryPartCFrame(CF(CF(FADE.PrimaryPart.Position,MOVEDIRECTION)*CF(0,0,-DIST/TIMER).p) * ANGLES(RAD(ORI.X), RAD(ORI.Y), RAD(ORI.Z)))
			end
		end
		FADE:remove()
	end))
end

function Raycast(POSITION, DIRECTION, RANGE, IGNOREDECENDANTS)
    return workspace:FindPartOnRay(Ray.new(POSITION, DIRECTION.unit * RANGE), IGNOREDECENDANTS)
end
 
function PositiveAngle(NUMBER)
    if NUMBER >= 0 then
        NUMBER = 0
    end
    return NUMBER
end
 
function NegativeAngle(NUMBER)
    if NUMBER <= 0 then
        NUMBER = 0
    end
    return NUMBER
end
 
function Swait(NUMBER)
    if NUMBER == 0 or NUMBER == nil then
        ArtificialHB.Event:wait()
    else
        for i = 1, NUMBER do
            ArtificialHB.Event:wait()
        end
    end
end
 
function QuaternionFromCFrame(cf)
    local mx, my, mz, m00, m01, m02, m10, m11, m12, m20, m21, m22 = cf:components()
    local trace = m00 + m11 + m22
    if trace > 0 then
        local s = math.sqrt(1 + trace)
        local recip = 0.5 / s
        return (m21 - m12) * recip, (m02 - m20) * recip, (m10 - m01) * recip, s * 0.5
    else
        local i = 0
        if m11 > m00 then
            i = 1
        end
        if m22 > (i == 0 and m00 or m11) then
            i = 2
        end
        if i == 0 then
            local s = math.sqrt(m00 - m11 - m22 + 1)
            local recip = 0.5 / s
            return 0.5 * s, (m10 + m01) * recip, (m20 + m02) * recip, (m21 - m12) * recip
        elseif i == 1 then
            local s = math.sqrt(m11 - m22 - m00 + 1)
            local recip = 0.5 / s
            return (m01 + m10) * recip, 0.5 * s, (m21 + m12) * recip, (m02 - m20) * recip
        elseif i == 2 then
            local s = math.sqrt(m22 - m00 - m11 + 1)
            local recip = 0.5 / s return (m02 + m20) * recip, (m12 + m21) * recip, 0.5 * s, (m10 - m01) * recip
        end
    end
end
 
function QuaternionToCFrame(px, py, pz, x, y, z, w)
    local xs, ys, zs = x + x, y + y, z + z
    local wx, wy, wz = w * xs, w * ys, w * zs
    local xx = x * xs
    local xy = x * ys
    local xz = x * zs
    local yy = y * ys
    local yz = y * zs
    local zz = z * zs
    return CFrame.new(px, py, pz, 1 - (yy + zz), xy - wz, xz + wy, xy + wz, 1 - (xx + zz), yz - wx, xz - wy, yz + wx, 1 - (xx + yy))
end
 
function QuaternionSlerp(a, b, t)
    local cosTheta = a[1] * b[1] + a[2] * b[2] + a[3] * b[3] + a[4] * b[4]
    local startInterp, finishInterp;
    if cosTheta >= 0.0001 then
        if (1 - cosTheta) > 0.0001 then
            local theta = ACOS(cosTheta)
            local invSinTheta = 1 / SIN(theta)
            startInterp = SIN((1 - t) * theta) * invSinTheta
            finishInterp = SIN(t * theta) * invSinTheta
        else
            startInterp = 1 - t
            finishInterp = t
        end
    else
        if (1 + cosTheta) > 0.0001 then
            local theta = ACOS(-cosTheta)
            local invSinTheta = 1 / SIN(theta)
            startInterp = SIN((t - 1) * theta) * invSinTheta
            finishInterp = SIN(t * theta) * invSinTheta
        else
            startInterp = t - 1
            finishInterp = t
        end
    end
    return a[1] * startInterp + b[1] * finishInterp, a[2] * startInterp + b[2] * finishInterp, a[3] * startInterp + b[3] * finishInterp, a[4] * startInterp + b[4] * finishInterp
end
 
function Clerp(a, b, t)
    local qa = {QuaternionFromCFrame(a)}
    local qb = {QuaternionFromCFrame(b)}
    local ax, ay, az = a.x, a.y, a.z
    local bx, by, bz = b.x, b.y, b.z
    local _t = 1 - t
    return QuaternionToCFrame(_t * ax + t * bx, _t * ay + t * by, _t * az + t * bz, QuaternionSlerp(qa, qb, t))
end
 
function CreateFrame(PARENT, TRANSPARENCY, BORDERSIZEPIXEL, POSITION, SIZE, COLOR, BORDERCOLOR, NAME)
    local frame = IT("Frame")
    frame.BackgroundTransparency = TRANSPARENCY
    frame.BorderSizePixel = BORDERSIZEPIXEL
    frame.Position = POSITION
    frame.Size = SIZE
    frame.BackgroundColor3 = COLOR
    frame.BorderColor3 = BORDERCOLOR
    frame.Name = NAME
    frame.Parent = PARENT
    return frame
end
 
function CreateLabel(PARENT, TEXT, TEXTCOLOR, TEXTFONTSIZE, TEXTFONT, TRANSPARENCY, BORDERSIZEPIXEL, STROKETRANSPARENCY, NAME)
    local label = IT("TextLabel")
    label.BackgroundTransparency = 1
    label.Size = UD2(1, 0, 1, 0)
    label.Position = UD2(0, 0, 0, 0)
    label.TextColor3 = TEXTCOLOR
    label.TextStrokeTransparency = STROKETRANSPARENCY
    label.TextTransparency = TRANSPARENCY
    label.FontSize = TEXTFONTSIZE
    label.Font = TEXTFONT
    label.BorderSizePixel = BORDERSIZEPIXEL
    label.TextScaled = false
    label.Text = TEXT
    label.Name = NAME
    label.Parent = PARENT
    return label
end
 
function NoOutlines(PART)
    PART.TopSurface, PART.BottomSurface, PART.LeftSurface, PART.RightSurface, PART.FrontSurface, PART.BackSurface = 10, 10, 10, 10, 10, 10
end
 
 
function CreateWeldOrSnapOrMotor(TYPE, PARENT, PART0, PART1, C0, C1)
    local NEWWELD = IT(TYPE)
    NEWWELD.Part0 = PART0
    NEWWELD.Part1 = PART1
    NEWWELD.C0 = C0
    NEWWELD.C1 = C1
    NEWWELD.Parent = PARENT
    return NEWWELD
end
 
function CreateSound(ID, PARENT, VOLUME, PITCH)
    local NEWSOUND = nil
    coroutine.resume(coroutine.create(function()
        NEWSOUND = IT("Sound", PARENT)
        NEWSOUND.Volume = VOLUME
        NEWSOUND.Pitch = PITCH
        NEWSOUND.SoundId = "http://www.roblox.com/asset/?id="..ID
        Swait()
        NEWSOUND:play()
        game:GetService("Debris"):AddItem(NEWSOUND, 10)
    end))
    return NEWSOUND
end
 
function CFrameFromTopBack(at, top, back)
    local right = top:Cross(back)
    return CF(at.x, at.y, at.z, right.x, top.x, back.x, right.y, top.y, back.y, right.z, top.z, back.z)
end
 
function CreateWave(SIZE,WAIT,CFRAME,DOESROT,ROT,COLOR,GROW)
    local wave = CreatePart(3, Effects, "Neon", 0, 0.5, BRICKC(COLOR), "Effect", VT(0,0,0))
    local mesh = IT("SpecialMesh",wave)
    mesh.MeshType = "FileMesh"
    mesh.MeshId = "http://www.roblox.com/asset/?id=20329976"
    mesh.Scale = SIZE
    mesh.Offset = VT(0,0,-SIZE.X/8)
    wave.CFrame = CFRAME
    coroutine.resume(coroutine.create(function(PART)
        for i = 1, WAIT do
            Swait()
            mesh.Scale = mesh.Scale + GROW
            mesh.Offset = VT(0,0,-(mesh.Scale.X/8))
            if DOESROT == true then
                wave.CFrame = wave.CFrame * CFrame.fromEulerAnglesXYZ(0,ROT,0)
            end
            wave.Transparency = wave.Transparency + (0.5/WAIT)
            if wave.Transparency > 0.99 then
                wave:remove()
            end
        end
    end))
end
 
function CreateWave2(SIZE,WAIT,CFRAME,DOESROT,ROT,COLOR,GROW)
    local wave = CreatePart(3, Effects, "Neon", 0, 0.5, BRICKC(COLOR), "Effect", VT(0,0,0))
    local mesh = IT("SpecialMesh",wave)
    mesh.MeshType = "FileMesh"
    mesh.MeshId = "http://www.roblox.com/asset/?id=20329976"
    mesh.Scale = SIZE
    --mesh.Offset = VT(0,0,-SIZE.X/8)
    wave.CFrame = CFRAME
    coroutine.resume(coroutine.create(function(PART)
        for i = 1, WAIT do
            Swait()
            mesh.Scale = mesh.Scale + GROW
            --mesh.Offset = VT(0,0,-(mesh.Scale.X/8))
            if DOESROT == true then
                wave.CFrame = wave.CFrame * CFrame.fromEulerAnglesXYZ(0,ROT,0)
            end
            wave.Transparency = wave.Transparency + (0.5/WAIT)
            if wave.Transparency > 0.99 then
                wave:remove()
            end
        end
    end))
end
 
function CreateSwirl(SIZE,WAIT,CFRAME,DOESROT,ROT,COLOR,GROW)
    local wave = CreatePart(3, Effects, "Neon", 0, 0.5, BRICKC(COLOR), "Effect", VT(0,0,0))
    local mesh = IT("SpecialMesh",wave)
    mesh.MeshType = "FileMesh"
    mesh.MeshId = "http://www.roblox.com/asset/?id=1051557"
    mesh.Scale = SIZE
    wave.CFrame = CFRAME
    coroutine.resume(coroutine.create(function(PART)
        for i = 1, WAIT do
            Swait()
            mesh.Scale = mesh.Scale + GROW
            mesh.Offset = VT(0,0,-(mesh.Scale.X/8))
            if DOESROT == true then
                wave.CFrame = wave.CFrame * CFrame.fromEulerAnglesXYZ(0,ROT,0)
            end
            wave.Transparency = wave.Transparency + (0.5/WAIT)
            if wave.Transparency > 0.99 then
                wave:remove()
            end
        end
    end))
end
 
 
function Slice(SIZE,WAIT,CFRAME,COLOR,GROW)
    local wave = CreatePart(3, Effects, "Neon", 0, 0.5, BRICKC(COLOR), "Effect", VT(1,1,1), true)
    local mesh = CreateMesh("SpecialMesh", wave, "FileMesh", "448386996", "", VT(0,SIZE/10,SIZE/10), VT(0,0,0))
    wave.CFrame = CFRAME
    coroutine.resume(coroutine.create(function(PART)
        for i = 1, WAIT do
            Swait()
            mesh.Scale = mesh.Scale * GROW
            wave.Transparency = wave.Transparency + (0.5/WAIT)
            if wave.Transparency > 0.99 then
                wave:remove()
            end
        end
    end))
end
 
function MagicSphere(SIZE,WAIT,CFRAME,COLOR,GROW)
    local wave = CreatePart(3, Effects, "Neon", 0, 0, BRICKC(COLOR), "Effect", VT(1,1,1), true)
    local mesh = IT("SpecialMesh",wave)
    mesh.MeshType = "Sphere"
    mesh.Scale = SIZE
    mesh.Offset = VT(0,0,0)
    wave.CFrame = CFRAME
    coroutine.resume(coroutine.create(function(PART)
        for i = 1, WAIT do
            Swait()
            mesh.Scale = mesh.Scale + GROW
            wave.Transparency = wave.Transparency + (1/WAIT)
            if wave.Transparency > 0.99 then
                wave:remove()
            end
        end
    end))
end
 
function MakeForm(PART,TYPE)
    if TYPE == "Cyl" then
        local MSH = IT("CylinderMesh",PART)
    elseif TYPE == "Ball" then
        local MSH = IT("SpecialMesh",PART)
        MSH.MeshType = "Sphere"
    elseif TYPE == "Wedge" then
        local MSH = IT("SpecialMesh",PART)
        MSH.MeshType = "Wedge"
    end
end
 
function CreateDebreeRing(FLOOR,POSITION,SIZE,BLOCKSIZE,SWAIT)
    if FLOOR ~= nil then
        coroutine.resume(coroutine.create(function()
            local PART = CreatePart(3, Effects, "Plastic", 0, 1, "Pearl", "DebreeCenter", VT(0,0,0))
            PART.CFrame = CF(POSITION)
            for i = 1, 45 do
                local RingPiece = CreatePart(3, Effects, "Plastic", 0, 0, "Pearl", "DebreePart", BLOCKSIZE)
                RingPiece.Material = FLOOR.Material
                RingPiece.Color = FLOOR.Color
                RingPiece.CFrame = PART.CFrame * ANGLES(RAD(0), RAD(i*8), RAD(0)) * CF(SIZE*4, 0, 0) * ANGLES(RAD(MRANDOM(-360,360)),RAD(MRANDOM(-360,360)),RAD(MRANDOM(-360,360)))
                Debris:AddItem(RingPiece,SWAIT/100)
            end
            PART:remove()
        end))
    end
end
 
function CheckTableForString(Table, String)
    for i, v in pairs(Table) do
        if string.find(string.lower(String), string.lower(v)) then
            return true
        end
    end
    return false
end
 
function CheckIntangible(Hit)
    local ProjectileNames = {"Water", "Arrow", "Projectile", "Effect", "Rail", "Lightning", "Bullet"}
    if Hit and Hit.Parent then
        if ((not Hit.CanCollide or CheckTableForString(ProjectileNames, Hit.Name)) and not Hit.Parent:FindFirstChild("Humanoid")) then
            return true
        end
    end
    return false
end
 
Debris = game:GetService("Debris")
 
function CastZapRay(StartPos, Vec, Length, Ignore, DelayIfHit)
    local Direction = CFrame.new(StartPos, Vec).lookVector
    local Ignore = ((type(Ignore) == "table" and Ignore) or {Ignore})
    local RayHit, RayPos, RayNormal = game:GetService("Workspace"):FindPartOnRayWithIgnoreList(Ray.new(StartPos, Direction * Length), Ignore)
    if RayHit and CheckIntangible(RayHit) then
        if DelayIfHit then
            wait()
        end
        RayHit, RayPos, RayNormal = CastZapRay((RayPos + (Vec * 0.01)), Vec, (Length - ((StartPos - RayPos).magnitude)), Ignore, DelayIfHit)
    end
    return RayHit, RayPos, RayNormal
end
 
function turnto(position)
    RootPart.CFrame=CFrame.new(RootPart.CFrame.p,VT(position.X,RootPart.Position.Y,position.Z)) * CFrame.new(0, 0, 0)
end

function CameraEnshaking(Length, Intensity)
	coroutine.resume(coroutine.create(function()
		local intensity = 1 * Intensity
		local rotM = 0.01 * Intensity
		for i = 0, Length, 0.1 do
			Swait()
			intensity = intensity - 0.05 * Intensity / Length
			rotM = rotM - 5.0E-4 * Intensity / Length
			hum.CameraOffset = Vector3.new(Rad(Mrandom(-intensity, intensity)), Rad(Mrandom(-intensity, intensity)), Rad(Mrandom(-intensity, intensity)))
			cam.CFrame = cam.CFrame * CF(Rad(Mrandom(-intensity, intensity)), Rad(Mrandom(-intensity, intensity)), Rad(Mrandom(-intensity, intensity))) * Euler(Rad(Mrandom(-intensity, intensity)) * rotM, Rad(Mrandom(-intensity, intensity)) * rotM, Rad(Mrandom(-intensity, intensity)) * rotM)
		end
		hum.CameraOffset = Vector3.new(0, 0, 0)
	end))
end


--//=================================\\
--||	     WEAPON CREATION
--\\=================================//



function SHAKECAM(POSITION, RANGE, INTENSITY, TIME)
	local TORSO = Torso
	local HUM = Humanoid
	if TORSO and RANGE >= (TORSO.Position - POSITION).Magnitude then
		coroutine.wrap(function()
			VT = Vector3.new
			MRANDOM = math.random
			local A = TIME
			local B = INTENSITY
			local C = true
			local HUMANOID = Humanoid
			local TIMER = A or 35
			local SHAKE = B or 5
			local FADE = C or true
			if HUMANOID then
				local FADER = SHAKE / TIMER
				for i = 1, TIMER do
					wait()
					HUMANOID.CameraOffset = VT(MRANDOM(-(SHAKE - FADER * i), SHAKE - FADER * i) / 10, MRANDOM(-(SHAKE - FADER * i), SHAKE - FADER * i) / 10, MRANDOM(-(SHAKE - FADER * i), SHAKE - FADER * i) / 10)
				end
				HUMANOID.CameraOffset = VT(0, 0, 0)
			end

		end)()
	end
end


--WACKYEFFECT({EffectType = "", Size = VT(1,1,1), Size2 = VT(0,0,0), Transparency = 0, Transparency2 = 1, CFrame = CF(), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1,1,1), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
function WACKYEFFECT(Table)
	local TYPE = (Table.EffectType or "Sphere")
	local SIZE = (Table.Size or VT(1,1,1))
	local ENDSIZE = (Table.Size2 or VT(0,0,0))
	local TRANSPARENCY = (Table.Transparency or 0)
	local ENDTRANSPARENCY = (Table.Transparency2 or 1)
	local CFRAME = (Table.CFrame or Torso.CFrame)
	local MOVEDIRECTION = (Table.MoveToPos or nil)
	local ROTATION1 = (Table.RotationX or 0)
	local ROTATION2 = (Table.RotationY or 0)
	local ROTATION3 = (Table.RotationZ or 0)
	local MATERIAL = (Table.Material or "Neon")
	local COLOR = (Table.Color or C3(1,1,1))
	local TIME = (Table.Time or 45)
	local SOUNDID = (Table.SoundID or nil)
	local SOUNDPITCH = (Table.SoundPitch or nil)
	local SOUNDVOLUME = (Table.SoundVolume or nil)
	coroutine.resume(coroutine.create(function()
		local PLAYSSOUND = false
		local SOUND = nil
		local EFFECT = CreatePart(3, Effects, MATERIAL, 0, TRANSPARENCY, BRICKC("Lime green"), "Effect", VT(1,1,1), true)
		if SOUNDID ~= nil and SOUNDPITCH ~= nil and SOUNDVOLUME ~= nil then
			PLAYSSOUND = true
			SOUND = CreateSound(SOUNDID, EFFECT, SOUNDVOLUME, SOUNDPITCH, false)
		end
		EFFECT.Color = COLOR
		local MSH = nil
		if TYPE == "Sphere" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "Sphere", "", "", SIZE, VT(0,0,0))
		elseif TYPE == "Block" then
			MSH = IT("BlockMesh",EFFECT)
			MSH.Scale = VT(SIZE.X,SIZE.X,SIZE.X)
		elseif TYPE == "Wave" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "20329976", "", SIZE, VT(0,0,-SIZE.X/8))
		elseif TYPE == "Ring" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "559831844", "", VT(SIZE.X,SIZE.X,0.1), VT(0,0,0))
		elseif TYPE == "Slash" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "662586858", "", VT(SIZE.X/10,0,SIZE.X/10), VT(0,0,0))
		elseif TYPE == "Round Slash" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "662585058", "", VT(SIZE.X/10,0,SIZE.X/10), VT(0,0,0))
		elseif TYPE == "Swirl" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "1051557", "", SIZE, VT(0,0,0))
		elseif TYPE == "Skull" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "4770583", "", SIZE, VT(0,0,0))
		elseif TYPE == "Crystal" then
			MSH = CreateMesh("SpecialMesh", EFFECT, "FileMesh", "9756362", "", SIZE, VT(0,0,0))
		end
		if MSH ~= nil then
			local MOVESPEED = nil
			if MOVEDIRECTION ~= nil then
				MOVESPEED = (CFRAME.p - MOVEDIRECTION).Magnitude/TIME
			end
			local GROWTH = SIZE - ENDSIZE
			local TRANS = TRANSPARENCY - ENDTRANSPARENCY
			if TYPE == "Block" then
				EFFECT.CFrame = CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))
			else
				EFFECT.CFrame = CFRAME
			end
			for LOOP = 1, TIME+1 do
				Swait()
				MSH.Scale = MSH.Scale - GROWTH/TIME
				if TYPE == "Wave" then
					MSH.Offset = VT(0,0,-MSH.Scale.X/8)
				end
				EFFECT.Transparency = EFFECT.Transparency - TRANS/TIME
				if TYPE == "Block" then
					EFFECT.CFrame = CFRAME*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)))
				else
					EFFECT.CFrame = EFFECT.CFrame*ANGLES(RAD(ROTATION1),RAD(ROTATION2),RAD(ROTATION3))
				end
				if MOVEDIRECTION ~= nil then
					local ORI = EFFECT.Orientation
					EFFECT.CFrame = CF(EFFECT.Position,MOVEDIRECTION)*CF(0,0,-MOVESPEED)
					EFFECT.Orientation = ORI
				end
			end
			if PLAYSSOUND == false then
				EFFECT:remove()
			else
				SOUND.Stopped:Connect(function()
					EFFECT:remove()
				end)
			end
		else
			if PLAYSSOUND == false then
				EFFECT:remove()
			else
				repeat Swait() until SOUND.Playing == false
				EFFECT:remove()
			end
		end
	end))
end


function CreateRing(SIZE, DOESROT, ROT, WAIT, CFRAME, COLOR, GROW)
	local wave = CreatePart(3, Effects, "Neon", 0, 0.5, BRICKC(COLOR), "Effect", VT(0, 0, 0))
	local mesh = CreateMesh("SpecialMesh", wave, "FileMesh", "559831844", "", SIZE, VT(0, 0, 0))
	wave.CFrame = CFRAME
	coroutine.resume(coroutine.create(function(PART)
		for i = 1, WAIT do
			Swait()
			mesh.Scale = mesh.Scale + GROW
			if DOESROT == true then
				wave.CFrame = wave.CFrame * CFrame.fromEulerAnglesXYZ(0, ROT, 0)
			end
			wave.Transparency = wave.Transparency + 0.5 / WAIT
			if wave.Transparency > 0.99 then
				wave:remove()
			end
		end
	end))
end
--EYE-

for i = 1, 35 do
	local FACE = CreatePart(3, FaceGradient, "Fabric", 0, 0+(i-1)/35.2, "Dark stone grey", "FaceGradient", VT(1.01,0.5,1.01),false)
	FACE.Color = C3(0,0,0)
	Head:FindFirstChildOfClass("SpecialMesh"):Clone().Parent = FACE
	CreateWeldOrSnapOrMotor("Weld", Head, Head, FACE, CF(0,0.35-(i-1)/75,0), CF(0, 0, 0))
end
--EYE1-
local Eye = CreatePart(3, Weapon, "Neon", 0, 0, "Really red", "Eye", VT(0.6,0.1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.2,0) * ANGLES(RAD(0), RAD(-18), RAD(15)), CF(0, 0, 0.4))
local Eye = CreatePart(3, Weapon, "Neon", 0, 0, "Really red", "Eye", VT(0.6,0.1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.2,0) * ANGLES(RAD(0), RAD(18), RAD(-15)), CF(0, 0, 0.4))
local Eye = CreatePart(3, Weapon, "Neon", 0, 0, "Really red", "Eye", VT(0.1,1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.15,0) * ANGLES(RAD(0), RAD(-18), RAD(0)), CF(0, 0, 0.4))
local Eye = CreatePart(3, Weapon, "Neon", 0, 0, "Really red", "Eye", VT(0.1,1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.15,0) * ANGLES(RAD(0), RAD(18), RAD(0)), CF(0, 0, 0.4))
--EYE2-
local Eye = CreatePart(3, Weapon2, "Neon", 0, 0, "Really black", "Eye", VT(0.6,0.1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.2,0) * ANGLES(RAD(0), RAD(-18), RAD(15)), CF(0, 0, 0.4))
local Eye = CreatePart(3, Weapon2, "Neon", 0, 0, "Really black", "Eye", VT(0.6,0.1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.2,0) * ANGLES(RAD(0), RAD(18), RAD(-15)), CF(0, 0, 0.4))
local Eye = CreatePart(3, Weapon2, "Neon", 0, 0, "Really black", "Eye", VT(0.1,1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.15,0) * ANGLES(RAD(0), RAD(-18), RAD(0)), CF(0, 0, 0.4))
local Eye = CreatePart(3, Weapon2, "Neon", 0, 0, "Really black", "Eye", VT(0.1,1,1)/2,false)
MakeForm(Eye,"Ball")
CreateWeldOrSnapOrMotor("Weld", Eye, Head, Eye, CF(0,0.15,0) * ANGLES(RAD(0), RAD(18), RAD(0)), CF(0, 0, 0.4))

--SS--



--EXE--
local EXE = Instance.new("Shirt")
EXE.ShirtTemplate = "rbxassetid://676428254"
EXE.Parent = Character
EXE.Name = "Cloth"
local LOL = Instance.new("Pants")
LOL.PantsTemplate = "rbxassetid://676428351"
LOL.Parent = Character
LOL.Name = "Cloth"

--DeadEye--

local Handle = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,1.2,0.2),false)
local RightArmGrasp = CreateWeldOrSnapOrMotor("Weld", Handle, RightArm, Handle, CF(0,-0.8, 0) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0.3, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,0.8,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, 0.2, 0.2) * ANGLES(RAD(0), RAD(180), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.3,0.5,0.6),false)
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.5, 0.4) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.4,0.4,0.4),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.45, 0.4) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
for i = 1, 8 do
	local Piece = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Eye", VT(0,0.35,0.41),false)
	CreateWeldOrSnapOrMotor("Weld", Handle, Part, Piece, CF(0, 0, 0) * ANGLES(RAD(0), RAD((360/8)*i), RAD(0)), CF(0, 0, 0))
end
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,0.5,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.55, 0.2) * ANGLES(RAD(-135), RAD(0), RAD(0)), CF(0, -0.3, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Eye", VT(0.39,0.41,0.39),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.45, 0.4) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.3,0.5,0.5),false)
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.5, 0.2) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.3,0.4,0.5),false)
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.55, 0.65) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,0,0.6),false)
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, 0, 0) * ANGLES(RAD(45), RAD(0), RAD(0)), CF(0, -0.2, -0.3))
local RightBarrel = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0.28,5,0.28),false)
MakeForm(RightBarrel,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, RightBarrel, CF(0, -0.6, 0.5) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, -2.5, 0))
local Part = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Part", VT(0,0.2,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, RightBarrel, Part, CF(0, 2.415, 0.15) * ANGLES(RAD(180), RAD(0), RAD(0)), CF(0, 0, 0))
local RightHole = CreatePart(3, DeadEye, "Metal", 0, 0, "Mid gray", "Eye", VT(0.2,0,0.2),false)
MakeForm(RightHole,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, RightBarrel, RightHole, CF(0, 2.5, 0), CF(0, 0, 0))



--Heads--


local Hair = CreatePart(3, Heads, "Fabric", 0, 0, "Gold", "Hair", VT(1,1,1),false)
local HairWeld1 = CreateWeldOrSnapOrMotor("Weld", Hair, Head, Hair, CF(0,0.1,-0.25) * ANGLES(RAD(0), RAD(0), RAD(0)), CF(0, 0, 0))
CreateMesh("SpecialMesh", Hair, "FileMesh", "873651376", "", VT(0.025, 0.025, 0.02), VT(0,0,0))
local Hair = CreatePart(3, Heads, "Fabric", 0, 0, "Gold", "Hair", VT(1,1,1),false)
local HairWeld2 = CreateWeldOrSnapOrMotor("Weld", Hair, Head, Hair, CF(0,0.2,-0.3) * ANGLES(RAD(0), RAD(0), RAD(0)), CF(0, 0, 0))
CreateMesh("SpecialMesh", Hair, "FileMesh", "873651376", "", VT(0.03, 0.015, 0.01), VT(0,0,-0.1))

 
--banblack--


--Ban--

local Handle = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,0.6,0.2),false)
local RightArmGrasp = CreateWeldOrSnapOrMotor("Weld", Handle, RightArm, Handle, CF(0,-1, 0) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0.21, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,0.5,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.3, 0.2) * ANGLES(RAD(0), RAD(180), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,0.3,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.4, 0) * ANGLES(RAD(0), RAD(0), RAD(180)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.3,0.3,0.3),false)
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.5, 0.2) * ANGLES(RAD(0), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.3,0.5,0.5),false)
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.5) * ANGLES(RAD(0), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.4,0.4,0.4),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.5) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
for i = 1, 8 do
	local Piece = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Eye", VT(0,0.35,0.41),false)
	CreateWeldOrSnapOrMotor("Weld", Handle, Part, Piece, CF(0, 0, 0) * ANGLES(RAD(0), RAD((360/8)*i), RAD(0)), CF(0, 0, 0))
end
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Eye", VT(0.38,0.41,0.38),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.5) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.37,0.5,0.37),false)
MakeForm(Part,"Ball")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.3) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.2,0.7,0.4),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.7, 0.5) * ANGLES(RAD(90), RAD(180), RAD(180)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.3,0.4,0.2),false)
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.7) * ANGLES(RAD(0), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.35,0.35,0.35),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.7) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.5,0.1,0.5),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 1) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.5,0.1,0.45),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 1.1) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "exe2", VT(0.2,0.5,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.65, -0.6) * ANGLES(RAD(135), RAD(180), RAD(180)), CF(0, -0.3, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.5,0.1,0.45),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 1.1) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "exe", VT(0.2,0.5,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.55, -0.6) * ANGLES(RAD(-135), RAD(0), RAD(0)), CF(0, -0.3, 0))
local LASTPART = Handle
for i = 1, 10 do
	if LASTPART == Handle then
		local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.1,0.2,0),false)
		LASTPART = Part
		CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.1, 0.2) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
	else
		local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.1,0.05,0),false)
		CreateWeldOrSnapOrMotor("Weld", Handle, LASTPART, Part, CF(0, 0.025, 0) * ANGLES(RAD(8), RAD(0), RAD(0)), CF(0, -0.025, 0))
		LASTPART = Part
	end
end

local Barrel = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.15,5,0.15),false)
MakeForm(Barrel,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Barrel, CF(0, -0.6, 1.8) * ANGLES(RAD(90), RAD(0), RAD(0)), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0.25,1,0.25),false)
MakeForm(Part,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Barrel, Part, CF(0, -0.6, 0), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0,0.1,0.2),false)
MakeForm(Part,"Wedge")
CreateWeldOrSnapOrMotor("Weld", Handle, Barrel, Part, CF(0, 2.4, 0.1) * ANGLES(RAD(180), RAD(0), RAD(0)), CF(0, 0, 0))
local Hole = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Eye", VT(0.125,0,0.125),false)
MakeForm(Hole,"Cyl")
CreateWeldOrSnapOrMotor("Weld", Handle, Barrel, Hole, CF(0, 2.5, 0), CF(0, 0, 0))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0, "Mid gray", "Part", VT(0,0,0),false)
local GEARWELD = CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.7), CF(0, 0, 0))
CreateMesh("SpecialMesh", Part, "FileMesh", 156292343, "", VT(0.8,0.8,1.5), VT(0,0,0.2))
local Part = CreatePart(3, Weapon2, "Metal", 0, 0.5, "Mid gray", "Eye", VT(0,0,0),false)
local GEARWELD2 = CreateWeldOrSnapOrMotor("Weld", Handle, Handle, Part, CF(0, -0.6, 0.7), CF(0, 0, 0))
CreateMesh("SpecialMesh", Part, "FileMesh", 156292343, "", VT(0.9,0.9,0.3), VT(0,0,0.2))
coroutine.resume(coroutine.create(function()
	while wait() do
		GEARWELD.C0 = GEARWELD.C0 * ANGLES(RAD(0), RAD(0), RAD(5))
		GEARWELD2.C0 = GEARWELD2.C0 * ANGLES(RAD(0), RAD(0), RAD(-5))
	end
end))


for _, c in pairs(Weapon:GetDescendants()) do
	if c.ClassName == "Part" and c.Name ~= "Eye" and c.Parent ~= Effects and c.Parent.Parent ~= Effects then
		c.Material = "Glass"
		c.Color = C3(0,0,0)
	elseif c.ClassName == "Part" and c.Name == "Eye" then
		c.Color = C3(1,0,0)
		c.Material = "Neon"
	end
end

for _, c in pairs(Weapon2:GetDescendants()) do
	if c.ClassName == "Part" and c.Name ~= "Eye" and c.Parent ~= Effects and c.Parent.Parent ~= Effects then
		c.Material = "Glass"
		c.Color = C3(0,0,0)
	elseif c.ClassName == "Part" and c.Name == "Eye" then
		c.Color = C3(0,0,0)
		c.Material = "Neon"
	end
end

for _, c in pairs(DeadEye:GetDescendants()) do
	if c.ClassName == "Part" and c.Name ~= "Eye" and c.Parent ~= Effects and c.Parent.Parent ~= Effects then
		c.Material = "Neon"
		c.Color = C3(0,0,0)
	elseif c.ClassName == "Part" and c.Name == "Eye" then
		c.Color = C3(1,0,0)
		c.Material = "Neon"
	end
end


for _, c in pairs(HM:GetDescendants()) do
	if c.ClassName == "Part" and c.Name ~= "Eye" and c.Parent ~= Effects and c.Parent.Parent ~= Effects then
		c.Material = "Neon"
		c.Color = C3(0,0,0)
	elseif c.ClassName == "Part" and c.Name == "Eye" then
		c.Color = C3(1,0,0)
		c.Material = "Neon"
	end
end

for _, c in pairs(Heads:GetDescendants()) do
	if c.ClassName == "Part" and c.Name ~= "Eye" and c.Parent ~= Effects and c.Parent.Parent ~= Effects then
		c.Material = "Neon"
		c.Color = C3(0,0,0)
	elseif c.ClassName == "Part" and c.Name == "Eye" then
		c.Color = C3(1,0,0)
		c.Material = "Neon"
	end
end

for _, c in pairs(Pan:GetDescendants()) do
	if c.ClassName == "Part" and c.Name ~= "Eye" and c.Parent ~= Effects and c.Parent.Parent ~= Effects then
		c.Material = "Neon"
		c.Color = C3(0,0,0)
	elseif c.ClassName == "Part" and c.Name == "Eye" then
		c.Color = C3(1,0,0)
		c.Material = "Neon"
	end
end

Weapon.Parent = Character
for _, c in pairs(Weapon:GetChildren()) do
	if c.ClassName == "Part" then
		c.CustomPhysicalProperties = PhysicalProperties.new(0, 0, 0, 0, 0)
	end
end


for _, c in pairs(Weapon:GetChildren()) do
	if c.ClassName == "Part" then
		c.CustomPhysicalProperties = PhysicalProperties.new(0, 0, 0, 0, 0)
	end
end

Weapon.Parent = Character
Weapon2.Parent = nil
DeadEye.Parent = nil
black.Parent = nil


function ParticleEmitter(Table)
	local PRTCL = Particle:Clone()
	local Speed = Table.Speed or 5
	local Drag = Table.Drag or 0
	local Size1 = Table.Size1 or 1
	local Size2 = Table.Size2 or 5
	local Lifetime1 = Table.Lifetime1 or 1
	local Lifetime2 = Table.Lifetime2 or 1.5
	local Parent = Table.Parent or Torso
	local Emit = Table.Emit or 100
	local Offset = Table.Offset or 360
	local Acel = Table.Acel or VT(0,0,0)
	local Enabled = Table.Enabled or false
	PRTCL.Parent = Parent
	PRTCL.Size = NumberSequence.new(Size1,Size2)
	PRTCL.Lifetime = NumberRange.new(Lifetime1,Lifetime2)
	PRTCL.Speed = NumberRange.new(Speed)
	PRTCL.VelocitySpread = Offset
	PRTCL.Drag = Drag
	PRTCL.Acceleration = Acel
	if Enabled == false then
		PRTCL:Emit(Emit)
		Debris:AddItem(PRTCL,Lifetime2)
	else
		PRTCL.Enabled = true
	end
	return PRTCL
end

Weapon.Parent = Character
for _, c in pairs(Weapon:GetChildren()) do
	if c.ClassName == "Part" then
		c.CustomPhysicalProperties = PhysicalProperties.new(0, 0, 0, 0, 0)
	end
end

local SKILLTEXTCOLOR = C3(0,0,0)
local SKILLFONT = "SciFi"
local SKILLTEXTSIZE = 7

Humanoid.Died:connect(function()
	ATTACK = true
end)


function printbye(Name)
	local MESSAGES = {"NameClan Jail, "}
	warn(MESSAGES[MRANDOM(1,#MESSAGES)]..Name..".")	
end

workspace.ChildAdded:connect(function(instance)
    for BANISH = 1, #TOBANISH do
		if TOBANISH[BANISH] ~= nil then
			if instance.Name == TOBANISH[BANISH] then
				coroutine.resume(coroutine.create(function()
					printbye(instance.Name)
					instance:ClearAllChildren()
					Debris:AddItem(instance,0.0005)
				end))
			end
		end
	end
end)

--//=================================\\
--||         DAMAGE FUNCTIONS
--\\=================================//
 
function StatLabel(LABELTYPE, CFRAME, TEXT, COLOR)
    local STATPART = CreatePart(3, Effects, "SmoothPlastic", 0, 1, "Really black", "Effect", VT())
    STATPART.CFrame = CF(CFRAME.p + VT(0, 1.5, 0))
    local BODYGYRO = IT("BodyGyro", STATPART)
    local BODYPOSITION = IT("BodyPosition", STATPART)
    BODYPOSITION.P = 2000
    BODYPOSITION.D = 100
    BODYPOSITION.maxForce = VT(math.huge, math.huge, math.huge)
    BODYPOSITION.position = STATPART.Position + VT(MRANDOM(-2, 2), 6, MRANDOM(-2, 2))
    game:GetService("Debris"):AddItem(STATPART ,5)
    local BILLBOARDGUI = Instance.new("BillboardGui", STATPART)
    BILLBOARDGUI.Adornee = STATPART
    BILLBOARDGUI.Size = UD2(2.5, 0, 2.5 ,0)
    BILLBOARDGUI.StudsOffset = VT(-2, 2, 0)
    BILLBOARDGUI.AlwaysOnTop = false
    local TEXTLABEL = Instance.new("TextLabel", BILLBOARDGUI)
    TEXTLABEL.BackgroundTransparency = 1
    TEXTLABEL.Size = UD2(2.5, 0, 2.5, 0)
    TEXTLABEL.Text = TEXT
    TEXTLABEL.Font = "SciFi"
    TEXTLABEL.FontSize="Size42"
    TEXTLABEL.TextColor3 = COLOR
    TEXTLABEL.TextStrokeTransparency = 1
    TEXTLABEL.TextScaled = true
    TEXTLABEL.TextWrapped = true
    coroutine.resume(coroutine.create(function(THEPART, THEBODYPOSITION, THETEXTLABEL)
        if LABELTYPE == "Normal" then
            for i = 1, 30 do
                Swait()
                STATPART.Position = STATPART.Position + VT(0, (15-i)/10 ,0)
                TEXTLABEL.TextTransparency = TEXTLABEL.TextTransparency + (1/30)
            end
        elseif LABELTYPE == "Debuff" then
            for i = 1, 30 do
                Swait()
                STATPART.Position = STATPART.Position - VT(0, i/10 ,0)
                TEXTLABEL.TextTransparency = TEXTLABEL.TextTransparency + (1/30)
            end
        elseif LABELTYPE == "Shock" then
            local ORIGIN = STATPART.Position
            for i = 1, 30 do
                Swait()
                STATPART.Position = ORIGIN + VT(MRANDOM(-2,2),MRANDOM(-2,2),MRANDOM(-2,2))
                TEXTLABEL.TextTransparency = TEXTLABEL.TextTransparency + (1/30)
            end
        end
        THEPART.Parent = nil
    end),STATPART, BODYPOSITION, TEXTLABEL)
end
--//=================================\\
--||			DAMAGING
--\\=================================//
--Pan--

function ApplyDamage(Humanoid,Damage,TorsoPart)
	local defence = Instance.new("BoolValue",Humanoid.Parent)
	defence.Name = ("HitBy"..Player.Name)
	game:GetService("Debris"):AddItem(defence, 0.001)
	Damage = Damage * DAMAGEMULTIPLIER
	if Humanoid.Health ~= 0 then
		local CritChance = MRANDOM(1,100)
		if Damage > Humanoid.Health then
			Damage = math.ceil(Humanoid.Health)
			if Damage == 0 then
				Damage = 0.1
			end
		end
		Humanoid.Health = Humanoid.Health - Damage
	end
end

function ApplyAoE(POSITION,RANGE,MINDMG,MAXDMG,FLING,INSTAKILL)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character and CHILD.Parent ~= Effects then
			local HUM = CHILD:FindFirstChildOfClass("Humanoid")
			if HUM then
				local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
				if TORSO then
					if (TORSO.Position - POSITION).Magnitude <= RANGE then
						if INSTAKILL == true then
							CHILD:BreakJoints()
						else
							local DMG = MRANDOM(MINDMG,MAXDMG)
							ApplyDamage(HUM,DMG,TORSO)
						end
						if FLING > 0 then
							for _, c in pairs(CHILD:GetChildren()) do
								if c:IsA("BasePart") then
									local bv = Instance.new("BodyVelocity") 
									bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
									bv.velocity = CF(POSITION,TORSO.Position).lookVector*FLING
									bv.Parent = c
									Debris:AddItem(bv,0.05)
								end
							end
						end
					end
				end
			end
		end
	end
end

function ApplyAoE3(POSITION,RANGE,MINDMG,MAXDMG,FLING,INSTAKILL)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character and CHILD.Parent ~= Effects then
			local HUM = CHILD:FindFirstChildOfClass("Humanoid")
			if HUM then
				local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
				if TORSO then
					if (TORSO.Position - POSITION).Magnitude <= RANGE then
						if INSTAKILL == true then
							CHILD:BreakJoints()
						else
							local DMG = MRANDOM(MINDMG,MAXDMG)
							ApplyDamage(HUM,DMG,TORSO)
						end
						if FLING > 0 then
							for _, c in pairs(CHILD:GetChildren()) do
								if c:IsA("BasePart") then
									local bv = Instance.new("BodyVelocity") 
									bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
									bv.velocity = CF(POSITION,TORSO.Position).lookVector*FLING
									bv.Parent = c
									Debris:AddItem(bv,0.05)
								end
							end
						end
					end
				end
			end
		end
	end
end

----

function KillChildren(v)
	v:BreakJoints()
	for _, c in pairs(v:GetChildren()) do
		if c:IsA("BasePart") then
			if c.Transparency < 1 then
				if c:FindFirstChildOfClass("Decal") then
					c:FindFirstChildOfClass("Decal"):remove()
				end
				particles(c)
				c.PE.Enabled = true
				c.Parent = Effects
				c.CanCollide = false
				c.Material = "Neon"
				c.Color = C3(1,0,0)
				c.Transparency = 1
				local grav = Instance.new("BodyPosition",c)
				grav.P = 20000
				grav.maxForce = Vector3.new(math.huge,math.huge,math.huge)
				grav.position = c.Position + VT(MRANDOM(-5,5),MRANDOM(-5,5),MRANDOM(-5,5))
				grav.Name = "GravityForce"
				coroutine.resume(coroutine.create(function()
					for i = 1, 20 do
						Swait()
						c.Transparency = c.Transparency + 1/20
					end
					c.PE.Enabled = false
					Debris:AddItem(c,2)
				end))
			end
		end
	end
end

function ApplyAoE(POSITION,RANGE)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character then
			local LISTED = false
			for LIST = 1, #WHITELIST do
				if WHITELIST[LIST] ~= nil then
					if CHILD.Name == WHITELIST[LIST] then
						LISTED = true
					end
				end
			end
			if LISTED == false then
				local HUM = CHILD:FindFirstChildOfClass("Humanoid")
				if HUM then
					local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
					if TORSO then
						if (TORSO.Position - POSITION).Magnitude <= RANGE+TORSO.Size.Magnitude then
							KillChildren(CHILD)
						end
					end
				end
			end
		end
	end
end

function killnearest(position,range,maxstrength,direction)
    for i,v in ipairs(workspace:GetChildren()) do
    local body = v:GetChildren()
        for part = 1, #body do
            if((body[part].ClassName == "Part" or body[part].ClassName == "MeshPart") and v ~= Character) then
                if(body[part].Position - position).Magnitude < range then
                    if v.ClassName == "Model" then
                        --v:BreakJoints()
                    end
                    local POS = position
                    coroutine.resume(coroutine.create(function()
                        body[part].Anchored = true
                        body[part].Parent = Effects
                        body[part].CanCollide = true
                        local SIZE = body[part].Size
                        body[part].Material = "Neon"
                        CreateSound("952306739", body[part], 2, MRANDOM(7, 12) / 10)
                        for i = 1, 75 do
                            Swait()
                            body[part].Transparency = 10
                            body[part].Color = C3(1,0,0)
                            body[part].Size = VT(0,0,0)
                            body[part].TextureID = ""
                            body[part].remove()
                        end
                        coroutine.resume(coroutine.create(function()

                        end))
                        body[part].Anchored = false
                        body[part].Velocity = direction.lookVector*maxstrength
                    end))
                end
            end
        end
        if v.ClassName == "Part" then
            if v.Anchored == false and (v.Position - position).Magnitude < range then
                local POS = position
                coroutine.resume(coroutine.create(function()
                    v.Anchored = true
                    v.Parent = Effects
                    local SIZE = v.Size
                    v.Material = "Neon"
                    CreateSound("952306739", v, 2, MRANDOM(7, 12) / 10)
                    v.Anchored = false
                    v.Velocity = direction.lookVector*maxstrength
                end))
            end
        end
    end
end

--//=================================\\
--||	    TRANSFORMATIONS
--\\=================================//

function Switch()
	ATTACK = true
	Rooted = true
	if MODE == "GoodCop" then
            for i=0, 0.3, 0.1 / Animation_Speed do
            Swait()
 RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
            for i=0, 3, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(15)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
                           Epic(4,4)
            for i=0, 0.5, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(5), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(10), RAD(0), RAD(65)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(10), RAD(0), RAD(-65)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(5), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(5), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
                           Epic(4,4)
            for i=0, 0.5, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(10), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(20), RAD(0), RAD(65)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(20), RAD(0), RAD(-65)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(10), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(10), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
                       Epic(4,4)
            for i=0, 0.5, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(15), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(30), RAD(0), RAD(65)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(30), RAD(0), RAD(-65)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(15), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(15), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
                       Epic(4,4)
            for i=0, 0.5, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(20), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(40), RAD(0), RAD(65)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(40), RAD(0), RAD(-65)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(20), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(20), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
                       Epic(4,4)
            for i=0, 0.5, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(25), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(50), RAD(0), RAD(65)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(50), RAD(0), RAD(-65)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(25), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(25), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
            for i=0, 0.5, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(25), RAD(0), RAD(0)), 2 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * LEFTSHOULDERC0, 2 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(25), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(25), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
            end
            local HITFLOOR,HITPOS,NORMAL = Raycast(RootPart.Position, (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 6 * Player_Size, Character)
            WACKYEFFECT({Time = 80, EffectType = "Wave", Size = VT(0,0,0), Size2 = VT(250,250,250), Transparency = 0.5, Transparency2 = 1, CFrame = RootPart.CFrame*CF(0,0,1), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1,0,0), SoundID = nil , SoundPitch = 1.2, SoundVolume = 4})
            WACKYEFFECT({Time = 60, EffectType = "Sphere", Size = VT(0,0,0), Size2 = VT(20,20,20), Transparency = 0.5, Transparency2 = 1, CFrame = RootPart.CFrame*CF(0,0,1), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1,0,0), SoundID = nil , SoundPitch = 1.2, SoundVolume = 4})
            Epic(6,6)
            coroutine.resume(coroutine.create(function()
            for e=1,#HITBODIES do
                Swait()
                if HITBODIES[e]~=nil then
                    local BOD = HITBODIES[e]
                    local TORS = BOD:FindFirstChild("Torso") or BOD:FindFirstChild("UpperTorso")
                    if TORS then
                        Slice("Thin",0.5,35,CF(TORS.Position,Torso.Position)*ANGLES(RAD(0),RAD(-90),RAD(0)),"Really red",VT(0.01,0,0.01))
                        local HUM = BOD:FindFirstChildOfClass("Humanoid")
                        if HUM then
                            ApplyDamage(HUM,3,2,1.3)
                        end
                    end
                end
            end
            HITBODIES = {}
        end))
            SprayBlood(Head.CFrame*CF(0,-Head.Size.Y,0).p,Head.CFrame*CF(MRANDOM(-15,15),-Head.Size.Y*2,MRANDOM(-15,15)).p,25,true,HITBODIES)
            for i=0, 1, 0.1 / Animation_Speed do
            Swait()
            SprayBlood(Head.CFrame*CF(0,-Head.Size.Y,0).p,Head.CFrame*CF(MRANDOM(-15,15),-Head.Size.Y*2,MRANDOM(-15,15)).p,25,true,HITBODIES)
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(-25), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(-25), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(-25), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
		MODE = "Killbot"
sick.SoundId = "rbxassetid://119591769079104"
Weapon.Parent = nil
Weapon2.Parent = nil
	elseif MODE == "Killbot" then
		for i=0, 1, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(-25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(-50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(-50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		MODE = "GoodCop"
sick.SoundId = "rbxassetid://90041337158549"
sick.TimePosition = 9
Weapon.Parent = Character
Weapon2.Parent = nil
	end
	ATTACK = false
	Rooted = false
end

function Switch4()
	ATTACK = true
	Rooted = true
	if MODE == "GoodCop" then
			-- Animation --
		coroutine.resume(coroutine.create(function()
		CameraEnshaking(5, 15)
		local HITFLOOR,HITPOS = Raycast(RootPart.Position, (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 25*SIZE, Character)
        ---WACKYEFFECT({Time = 30, EffectType = "Sphere", Size = VT(75,75,75), Size2 = VT(0.5,0.5,0.5), Transparency = 0, Transparency2 = 0.6, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(255/255, 0/255, 0/255), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
		Swait(25)
       ---ApplyAoE(HITPOS,10,5,10,20,false)
        WACKYEFFECT({Time = 10, EffectType = "Wave", Size = VT(0.45,0.11,0.45)*SIZE, Size2 = VT(12,3,12), Transparency = 0, Transparency2 = 1, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(159/255, 0/255, 0/255), SoundID = 743521450, SoundPitch = 1, SoundVolume = 6})
        Swait(7.5)
        ---ApplyAoE(HITPOS,10,5,10,20,false)
        ---WACKYEFFECT({Time = 10, EffectType = "Wave", Size = VT(0.45,0.11,0.45)*SIZE, Size2 = VT(12,3,12), Transparency = 0, Transparency2 = 1, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(159/255, 0/255, 0/255), SoundID = 743521450, SoundPitch = 1, SoundVolume = 6})
        Swait(7.5)
        ---ApplyAoE(HITPOS,10,5,10,20,false)
        ---WACKYEFFECT({Time = 10, EffectType = "Wave", Size = VT(0.45,0.11,0.45)*SIZE, Size2 = VT(12,3,12), Transparency = 0, Transparency2 = 1, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(159/255, 0/255, 0/255), SoundID = 743521450, SoundPitch = 1, SoundVolume = 6})
        Swait(7.5)
        ---ApplyAoE(HITPOS,10,5,10,20,false)
        ---WACKYEFFECT({Time = 10, EffectType = "Wave", Size = VT(0.45,0.11,0.45)*SIZE, Size2 = VT(12,3,12), Transparency = 0, Transparency2 = 1, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(159/255, 0/255, 0/255), SoundID = 743521450, SoundPitch = 1, SoundVolume = 6})
		end))
	sick.SoundId = "rbxassetid://101266623917319"
		for i=0, 1.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(80), RAD(75), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		for i=0, 1.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		MODE = "Superior"
Weapon.Parent = nil
Weapon2.Parent = nil
     elseif MODE == "Superior" then
		for i=0, 1, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(-25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(-50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(-50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		MODE = "GoodCop"
sick.SoundId = "rbxassetid://134147704384391"
sick.TimePosition = 9
Weapon.Parent = Character
Weapon2.Parent = nil
	end
	ATTACK = false
	Rooted = false
end


function Switch2()
	ATTACK = true
	Rooted = true
	if MODE == "GoodCop" then
		for i=0, 0.3, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		for i=0, 1, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.5, -0.5) * ANGLES(RAD(100), RAD(0), RAD(-70)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35, -0.35) * ANGLES(RAD(70), RAD(0), RAD(80)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.6, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5), RAD(25), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.5, -0.5) * ANGLES(RAD(100), RAD(0), RAD(-50)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35, -0.35) * ANGLES(RAD(70), RAD(0), RAD(60)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.6, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5), RAD(-25), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.5, -0.5) * ANGLES(RAD(100), RAD(0), RAD(-90)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35, -0.35) * ANGLES(RAD(70), RAD(0), RAD(90)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.6, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5), RAD(25), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.5, -0.5) * ANGLES(RAD(100), RAD(0), RAD(-50)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35, -0.35) * ANGLES(RAD(70), RAD(0), RAD(60)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.6, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5), RAD(-25), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.5, -0.5) * ANGLES(RAD(100), RAD(0), RAD(-90)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35, -0.35) * ANGLES(RAD(70), RAD(0), RAD(90)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		MODE = "DeadEye"
sick.SoundId = "rbxassetid://90041337158549"
sick.TimePosition = 0
Weapon.Parent = nil
Weapon2.Parent = nil
DeadEye.Parent = Character
	elseif MODE == "DeadEye" then
		for i=0, 1, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(-25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(-50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(-50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		MODE = "GoodCop"
sick.SoundId = "rbxassetid://106767299215726"
sick.TimePosition = 9
Weapon.Parent = Character
Weapon2.Parent = nil
DeadEye.Parent = nil
	end
	ATTACK = false
	Rooted = false
end

function Switch3()
	ATTACK = true
	Rooted = true
	if MODE == "GoodCop" then
		-- Animation --
		 local HITFLOOR,HITPOS = Raycast(RootPart.Position, (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 25*SIZE, Character)
        coroutine.resume(coroutine.create(function()
        WACKYEFFECT({Time = 50, EffectType = "Box", Size = VT(8.5,0,8.5), Size2 = VT(8.5,12.5,8.5), Transparency = 0.3, Transparency2 = 0, CFrame = RootPart.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(153, 0/255, 153), SoundID = nil, SoundPitch = 0.9, SoundVolume = 8})
        wait(0.8)
        WACKYEFFECT({Time = 60, EffectType = "Box", Size = VT(8.5,12.5,8.5), Size2 = VT(8.5,0,8.5), Transparency = 0, Transparency2 = 1, CFrame = RootPart.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(153, 0/255, 153), SoundID = nil, SoundPitch = 0.9, SoundVolume = 8})
		wait(0.7)
        WACKYEFFECT({Time = 185, EffectType = "Box", Size = VT(12,0.22,12)*2, Size2 = VT(0,0.22,0), Transparency = 1, Transparency2 = 0, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(153, 0/255, 153), SoundID = 1820267018, SoundPitch = 0.9, SoundVolume = 8})
        CameraEnshaking(2, 35)
        WACKYEFFECT({Time = 105, EffectType = "Block", Size = VT(0,0,0), Size2 = VT(70,70,70)*2, Transparency = 0, Transparency2 = 1, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(153, 0/255, 153), SoundID = 144699494, SoundPitch = 1, SoundVolume = 15})
        ---ApplyAoE(HITPOS,50,25,75,30,false)
        WACKYEFFECT({Time = 80, EffectType = "Wave", Size = VT(0,0.11,0), Size2 = VT(500,0.11,500), Transparency = 0, Transparency2 = 1, CFrame = CF(HITPOS), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1, 1, 1), SoundID = nil, SoundPitch = 1, SoundVolume = 15})
        end))
		sick.SoundId = "rbxassetid://134970366973445"
       for i=0, 1.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(-15), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(20), RAD(0), RAD(140)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-20), RAD(0), RAD(-140)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(-15), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(-15), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		for i=0, 3, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 9999 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(-15), RAD(0), RAD(0)), 2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(50), RAD(125)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(-50), RAD(-125)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.71) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-25)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.4) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(30)), 1 / Animation_Speed)
		end
		for i=0, 1.8, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.9 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(135), RAD(0), RAD(0)), 0.6 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(-35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.15*SIZE, 0.4*SIZE, 0.5) * ANGLES(RAD(-50), RAD(0), RAD(-45)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.95*SIZE, -0.85) * ANGLES(RAD(150), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), 0.09) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.41) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		MODE = "black"
Weapon.Parent = nil
Weapon2.Parent = nil
DeadEye.Parent = nil
black.Parent = Character
	elseif MODE == "black" then
		for i=0, 1, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(-25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(-50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(-50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		MODE = "GoodCop"
sick.SoundId = "rbxassetid://124255779263026"
sick.TimePosition = 9
Weapon.Parent = Character
Weapon2.Parent = nil
DeadEye.Parent = nil
black.Parent = nil
	end
	ATTACK = false
	Rooted = false
end


function Epic(SHAKE,TIMER)
    coroutine.resume(coroutine.create(function()
        local FADER = SHAKE/TIMER
        for i = 1, TIMER do
            wait()
            Humanoid.CameraOffset = VT(MRANDOM(-(SHAKE-(FADER*i)),(SHAKE-(FADER*i)))/10,MRANDOM(-(SHAKE-(FADER*i)),(SHAKE-(FADER*i)))/10,MRANDOM(-(SHAKE-(FADER*i)),(SHAKE-(FADER*i)))/10)
        end
        Humanoid.CameraOffset = VT(0,0,0)
    end))
end


function PuddleOfBlood(Position,MaxDrop,Model,MaxSize)
    local HITFLOOR, HITPOS, NORMAL = Raycast(Position, (CF(Position, Position + VT(0, -1, 0))).lookVector, MaxDrop, Model)
    if HITFLOOR ~= nil then
        if HITFLOOR.Parent ~= Effects and HITFLOOR.Parent ~= Character then
            if HITFLOOR.Name == "BloodPuddle" then
                local DIST = (Position - HITFLOOR.Position).Magnitude
                if (HITFLOOR.Size.Z <= 5 and HITFLOOR.Size.Z < MaxSize) or (HITFLOOR.Size.Z > 5 and HITFLOOR.Size.Z < MaxSize and DIST < HITFLOOR.Size.Z/3) then
                    HITFLOOR.Size = HITFLOOR.Size + VT(0.1,0,0.1)
                end
            else
                if HITFLOOR.Anchored == true then
                    local BLOOD = CreatePart(3, Effects, "Glass", 0, 0, "Maroon", "BloodPuddle", VT(2,0,2))
                    BLOOD.CFrame = CF(HITPOS,HITPOS+NORMAL)*ANGLES(RAD(90),RAD(0),RAD(0))
                    MakeForm(BLOOD,"Cyl")
                    coroutine.resume(coroutine.create(function()
                        Swait(75)
                        while true do
                            Swait()
                            BLOOD.Size = BLOOD.Size - VT(0.02,0,0.02)
                            if BLOOD.Size.Z < 0.051 then
                                BLOOD:remove()
                                break
                            end
                        end
                    end))
                end
            end
        end
    end
end


function SprayBlood(POSITION,DIRECTION,BloodSize,DoesTable,Table)
    local BLOOD = CreatePart(3, Effects, "Glass", 0, 0, "Maroon", "BloodPuddle", VT(0.5,0.5,0.5),false)
    BLOOD.CFrame = CF(POSITION)
    MakeForm(BLOOD,"Ball")
    local bv = Instance.new("BodyVelocity",BLOOD)
    bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
    bv.velocity = CF(POSITION,DIRECTION+VT(MRANDOM(-3,3),MRANDOM(-3,3)/30,MRANDOM(-3,3))).lookVector*65
    bv.Name = "MOVE"
    Debris:AddItem(bv,0.05)
    coroutine.resume(coroutine.create(function()
        local HASTOUCHEDGROUND = false
        local HIT = BLOOD.Touched:Connect(function(hit)
            if hit.Anchored == true then
                HASTOUCHEDGROUND = true
                if DoesTable == true then
                    AddChildrenToTable(BLOOD.Position,workspace,15,Table)
                end
                PuddleOfBlood(BLOOD.Position+VT(0,1,0),2,BLOOD,BloodSize)
            end
        end)
        for i = 1, 75 do
            Swait()
            if i > 5 then
                BLOOD.CanCollide = true
            end
            if HASTOUCHEDGROUND == true then
                break
            end
        end
        BLOOD:remove()
    end))
end
 
function AddChildrenToTable(FROM,PARENT,DIST,TABLE)
    for _, c in pairs(PARENT:GetChildren()) do
        if c.ClassName == "Model" then
            if c ~= Character and c:FindFirstChildOfClass("Humanoid") and (c:FindFirstChild("Torso") or c:FindFirstChild("UpperTorso")) then
                local HUMANOID = c:FindFirstChildOfClass("Humanoid")
                local TORSO = (c:FindFirstChild("Torso") or c:FindFirstChild("UpperTorso"))
                if (TORSO.Position - FROM).Magnitude < DIST then
                    table.insert(TABLE,c)
                end
                AddChildrenToTable(FROM,c,DIST,TABLE)
            elseif c.ClassName == "Folder" then
                AddChildrenToTable(FROM,c,DIST,TABLE)
            end
        end
    end
end

--//=================================\\
--||	ATTACK FUNCTIONS AND STUFF
--\\=================================//


function PAW_HAHA(LOC,AIMTO,OUCH)
    WACKYEFFECT({Time = 25, EffectType = "Block", Size = VT(0,0,0), Size2 = VT(1.4,1.4,1.4), Transparency = 0, Transparency2 = 1, CFrame = CF(LOC), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = BRICKC"New Yeller".Color, SoundID = 138083993, SoundPitch = MRANDOM(8,12)/13, SoundVolume = 2})
    for i = 1, 4 do
        local POS1 = CF(LOC,AIMTO)*CF(0,0,-45).p
        local AIMPOS = CF(LOC,POS1) * CF(0,0,-45) * ANGLES(RAD(MRANDOM(0,360)), RAD(MRANDOM(0,360)), RAD(MRANDOM(0,360)))*CF(0,0,MRANDOM(5,75)/10).p
        local HIT,POS = CastProperRay(LOC,AIMPOS,1000,Character)
        local DISTANCE = (POS - LOC).Magnitude
        if HIT then
            local HUM = nil
            if HIT.Parent:FindFirstChildOfClass("Humanoid") then
                HUM = HIT.Parent:FindFirstChildOfClass("Humanoid")
            elseif HIT.Parent.Parent:FindFirstChildOfClass("Humanoid") then
                HUM = HIT.Parent.Parent:FindFirstChildOfClass("Humanoid")
            end
            if HUM then
                ApplyDamage(HUM,OUCH+MRANDOM(-3,3))
            end
        end
        WACKYEFFECT({Time = 20, EffectType = "Block", Size = VT(0,0,0), Size2 = VT(1,1,1), Transparency = 0, Transparency2 = 1, CFrame = CF(POS), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = BRICKC"New Yeller".Color, SoundID = nil, SoundPitch = 1, SoundVolume = 4})
        WACKYEFFECT({Time = 20, EffectType = "Box", Size = VT(0,0,DISTANCE), Size2 = VT(0.7,0.7,DISTANCE), Transparency = 0.6, Transparency2 = 1, CFrame = CF(LOC,POS)*CF(0,0,-DISTANCE/2), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = BRICKC"New Yeller".Color, SoundID = nil, SoundPitch = nil, SoundVolume = nil})
    end
end

function OhnoItsAShotgun()
    local SHOOTgun = CreatePart(3, Effects, "Fabric", 0, 1, BRICKC("Black"), "OhNoItsAShotgun", VT(0.25, 0.5, 5), true)
    CreateMesh("SpecialMesh", SHOOTgun, "FileMesh", "3835506", "", VT(0.9, 1.13, 1), VT(0,-0.3,0))
    return SHOOTgun
end

local TOBANISH = {}

function Intro()
	ATTACK = true
	Rooted = true
coroutine.resume(coroutine.create(function()
	    wait(7)

        Swait(30)
Weapon.Parent = Character
        Swait(30)
Weapon.Parent = nil
Weapon2.Parent = Character
        Swait(30)
Weapon2.Parent = nil
DeadEye.Parent = Character
        Swait(30)
DeadEye.Parent = nil
Weapon.Parent = Character
		end))
	repeat 
		Swait() 
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1*SIZE) - 1)) * ANGLES(RAD(15 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 0.15 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-3.5 * COS(SINE / 12)), RAD(-45), RAD(12)) * ANGLES(RAD(-45 - 2.5 * COS(SINE / 12) + 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.1 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.8 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(0), RAD(45), RAD(-12)) * ANGLES(RAD(-45 - 2.5 * COS(SINE / 12) + 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.1 * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(75), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(2.5 * COS(SINE / 12))), 0.8 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.1 * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(-75), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0 - 2.5 * COS(SINE / 12))), 0.8 / Animation_Speed)
	until sick.TimePosition > 14
	repeat 
		Swait()
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, 0 * SIZE, -1 * SIZE) * ANGLES(RAD(45), RAD(0), RAD(0)), 0.2 / Animation_Speed/8)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * SIZE, 0 * SIZE, 0 + ((1 * SIZE) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * SIZE, 1 * SIZE, 1 * SIZE) * ANGLES(RAD(30), RAD(-690), RAD(2 - 2.5 * COS(SINE / 12) + 2.5 * SIN(SINE / 24))) * RIGHTSHOULDERC0, 0.15 / Animation_Speed/8)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE, 0 * SIZE) * ANGLES(RAD(90), RAD(25), RAD(45)) * LEFTSHOULDERC0, 0.15 / Animation_Speed/8)
			RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -0.3 * SIZE, -1 * SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-20)), 0.2 / Animation_Speed/8)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -0.3 * SIZE, 0 * SIZE) * ANGLES(RAD(0), RAD(5), RAD(0)) * ANGLES(RAD(45), RAD(0), RAD(10)), 0.2 / Animation_Speed/8)
	until sick.TimePosition > 16
	repeat 
		Swait() 
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, 0 * SIZE, -1 * SIZE) * ANGLES(RAD(45), RAD(0), RAD(0)), 0.2 / Animation_Speed*3)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * SIZE, 0 * SIZE, 0 + ((1 * SIZE) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 1*SIZE, -0.3*SIZE) * ANGLES(RAD(0.98), RAD(-119.59 + 2.5 * SIN(SINE / 25)), RAD(-175.39 + 2.5 * SIN(SINE / 12))) * RIGHTSHOULDERC0, 0.2 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE, 0 * SIZE) * ANGLES(RAD(90), RAD(25), RAD(45)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -0.3 * SIZE, -1 * SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-20)), 0.2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -0.3 * SIZE, 0 * SIZE) * ANGLES(RAD(0), RAD(5), RAD(0)) * ANGLES(RAD(45), RAD(0), RAD(10)), 0.2 / Animation_Speed)
	until sick.TimePosition > 20.7
	TRANSFORMED = true
	ATTACK = false
	Rooted = false
end

function InsaneCameraText()
	local FRAME = CreateFrame(WEAPONGUI, 1, 2, UD2(0, 0, 0, 0), UD2(0.13, 0, 0.07, 0), C3(0,0,0), C3(0, 0, 0), "SNAP!!")
local FACEME = {"PLZZZZ","MAX-LOCAL DANG","HAHAHA","BY NAMECLAN??!","IWANNAEATTOMYUM","AM I PRETTY?","YA HAHAH","PANDORA EDIT??!!","I CAN'T DO IT ANYMORE","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS","PLZ DON'T LEAK THIS"}
local INSANITYGUIS = {}
for e = 1, 28 do
    for i = 1, 22 do
        local MURDERFRAME = FRAME:Clone()
        MURDERFRAME.Position = UD2(-0.05+i/30, 0, e/30, 0)
        MURDERFRAME.Parent = WEAPONGUI
        table.insert(INSANITYGUIS,MURDERFRAME)
    end
end
coroutine.resume(coroutine.create(function()
    while true do
        wait()
        coroutine.resume(coroutine.create(function()
            local COLOR = C3(MRANDOM(100,255)/255,0,0)
            local APPEARTEXT = FACEME[MRANDOM(1,#FACEME)]
            local SHOW = ""
            for i = 1,string.len(APPEARTEXT),1 do
                local STRING = string.sub(APPEARTEXT,i,i)
                if MRANDOM(1,2) == 1 then
                    SHOW = SHOW..string.lower(STRING)
                else
                    SHOW = SHOW..STRING
                end
            end
            local PARENT = INSANITYGUIS[MRANDOM(1,#INSANITYGUIS)]
            local TEXT = CreateLabel(PARENT, SHOW, COLOR, 7, SKILLFONT, 1, 2, 1, "THISISYOURFAULTH")
            for i = 1, 15 do
                Swait()
                TEXT.Rotation = MRANDOM(-15,15)
                TEXT.TextTransparency = TEXT.TextTransparency - 1/15
            end
            for i = 1, 15 do
                Swait()
                TEXT.Rotation = MRANDOM(-15,15)
                TEXT.TextTransparency = TEXT.TextTransparency + 1/15
            end
            TEXT:Remove()
        end))
    end
end))
FRAME:remove()
end


function swait(num)
	if num == 0 or num == nil then
		ArtificialHB.Event:wait()
	else
		for i = 0, num do
			ArtificialHB.Event:wait()
		end
	end
end

function Chatter(Text,Timer)
    local chat = coroutine.wrap(function()
        if Character:FindFirstChild("SpeechBoard")~= nil then
            Character:FindFirstChild("SpeechBoard"):destroy()
        end
        local naeeym2 = IT("BillboardGui",Character)
        naeeym2.Size = UD2(0,100,0,40)
        naeeym2.StudsOffset = Vector3.new(0,2,0)
        naeeym2.Adornee = Character.Head
        naeeym2.Name = "SpeechBoard"
        naeeym2.AlwaysOnTop = true
        local tecks2 = IT("TextLabel",naeeym2)
        tecks2.BackgroundTransparency = 1
        tecks2.BorderSizePixel = 0
        tecks2.Text = ""
        tecks2.Font = "Legacy"
        tecks2.TextSize = 15
        tecks2.TextStrokeTransparency = 0
        tecks2.TextColor3 = Color3.new(1,1,1)
        tecks2.TextStrokeColor3 = Color3.new(0,0,0)
        tecks2.Size = UDim2.new(1,0,0.5,0)
        for i = 1,string.len(Text),1 do
            CreateSound(418252437, Head, 3, MRANDOM(8,12)/15, false)
            tecks2.Text = string.sub(Text,1,i)
            wait(Timer)
        end
        wait(1)
        naeeym2:Destroy()
    end)
    chat()
end

function chatfunc(text)
    local chat = coroutine.wrap(function()
    if Character:FindFirstChild("TalkingBillBoard")~= nil then
        Character:FindFirstChild("TalkingBillBoard"):destroy()
    end
    local Bill = Instance.new("BillboardGui",Character)
    Bill.Size = UDim2.new(0,100,0,40)
    Bill.StudsOffset = Vector3.new(0,3,0)
    Bill.Adornee = Character.Head
    Bill.Name = "TalkingBillBoard"
    local Hehe = Instance.new("TextLabel",Bill)
    Hehe.BackgroundTransparency = 1
    Hehe.BorderSizePixel = 0
    Hehe.Text = ""
    Hehe.Font = "Fantasy"
    Hehe.TextSize = 40
    Hehe.TextStrokeTransparency = 0
    Hehe.Size = UDim2.new(1,0,0.5,0)
    coroutine.resume(coroutine.create(function()
        while Hehe ~= nil do
            wait()
            Hehe.Position = UDim2.new(math.random(-.4,.4),math.random(-5,5),.05,math.random(-5,5))
            Hehe.Rotation = math.random(-5,5)
            Hehe.TextColor3 = Color3.new(1,0,0)
            Hehe.TextStrokeColor3 = Color3.new(1,0,0)
        end
    end))
    for i = 1,string.len(text),1 do
        wait()
        Hehe.Text = string.sub(text,1,i)
    end
    wait(1.5)--Re[math.random(1, 93)]
    for i = 0, 1, .025 do
        wait()
        Bill.ExtentsOffset = Vector3.new(math.random(-i, i), math.random(-i, i), math.random(-i, i))
        Hehe.TextStrokeTransparency = i
        Hehe.TextTransparency = i
    end
    Bill:Destroy()
    end)
chat()
end
 
function onChatted(msg)
    chatfunc(msg)
end
 
chatfunc("Hmmmmmm...")

function printbye(Name)
	local MESSAGES = {"BE GONE FROM HERE AND DONT RETURN, "}
	chatfunc(MESSAGES[MRANDOM(1,#MESSAGES)]..Name..".")	
end

workspace.ChildAdded:connect(function(instance)
    for BANISH = 1, #TOBANISH do
		if TOBANISH[BANISH] ~= nil then
			if instance.Name == TOBANISH[BANISH] then
				coroutine.resume(coroutine.create(function()
					printbye(instance.Name)
					instance:ClearAllChildren()
					Debris:AddItem(instance,0.0005)
				end))
			end
		end
	end
end)

function Banish(Foe)
	if Foe then
		coroutine.resume(coroutine.create(function()
			--if game.Players:FindFirstChild(Foe.Name) then
				table.insert(TOBANISH,Foe.Name)
				printbye(Foe.Name)
			--end
			Foe.Archivable = true
			local CLONE = Foe:Clone()
			Foe:Destroy()
			CLONE.Parent = Effects
			CLONE:BreakJoints()
			local MATERIALS = {"Glass","Neon"}
			for _, c in pairs(CLONE:GetDescendants()) do
				if c:IsA("BasePart") then
					if c.Name == "Torso" or c.Name == "UpperTorso" or c == CLONE.PrimaryPart then
 						CreateSound(340722848, c, 10, 1, false)
					end
					c.Anchored = true
					c.Transparency = c.Transparency + 0.2
					c.Material = MATERIALS[MRANDOM(1,2)]
					c.Color = C3(0,255,0)
					if c.ClassName == "MeshPart" then
						c.TextureID = ""
					end
					if c:FindFirstChildOfClass("SpecialMesh") then
						c:FindFirstChildOfClass("SpecialMesh").TextureId = ""
					end
					if c:FindFirstChildOfClass("Decal") then
						c:FindFirstChildOfClass("Decal"):remove()
					end
					c.Name = "Banished"
					c.CanCollide = false
				else
					c:remove()
				end
			end
			local A = false
			for i = 1, 35 do
				if A == false then
					A = true
				elseif A == true then
					A = false
				end
				for _, c in pairs(CLONE:GetDescendants()) do
					if c:IsA("BasePart") then
						c.Anchored = true
						c.Material = MATERIALS[MRANDOM(1,2)]
						c.Transparency = c.Transparency + 0.8/35
						if A == false then
							c.CFrame = c.CFrame*CF(MRANDOM(-45,45)/45,MRANDOM(-45,45)/45,MRANDOM(-45,45)/45)
						elseif A == true then
							c.CFrame = c.CFrame*CF(MRANDOM(-45,45)/45,MRANDOM(-45,45)/45,MRANDOM(-45,45)/45)						
						end
					end
				end
				Swait()
			end
			CLONE:remove()
		end))
	end
end

function CastProperRay(StartPos, EndPos, Distance, Ignore)
	local DIRECTION = CF(StartPos,EndPos).lookVector
	return Raycast(StartPos, DIRECTION, Distance, Ignore)
end

function SpawnTrail(FROM,TO,BIG)
	local TRAIL = CreatePart(3, Effects, "Neon", 0, 0.5, "Really black", "Trail", VT(0,0,0))
	MakeForm(TRAIL,"Cyl")
	local DIST = (FROM - TO).Magnitude
	if BIG == true then
		TRAIL.Size = VT(0.15,DIST,0.15)
	else
		TRAIL.Size = VT(0.45,DIST,0.45)
	end
	TRAIL.CFrame = CF(FROM, TO) * CF(0, 0, -DIST/2) * ANGLES(RAD(90),RAD(0),RAD(0))
	coroutine.resume(coroutine.create(function()
		for i = 1, 5 do
			Swait()
			TRAIL.Transparency = TRAIL.Transparency + 0.1
		end
		TRAIL:remove()
	end))
end

local asd = Instance.new("ParticleEmitter")
asd.Color = ColorSequence.new(Color3.new(0.5, 0, 0), Color3.new(.3, 0, 0))
asd.LightEmission = .1
asd.Texture = "http://www.roblox.com/asset/?ID=291880914"
aaa = NumberSequence.new({NumberSequenceKeypoint.new(0, 0.6),NumberSequenceKeypoint.new(1, 2)})
bbb = NumberSequence.new({NumberSequenceKeypoint.new(0, 1),NumberSequenceKeypoint.new(0.0636, 0), NumberSequenceKeypoint.new(1, 1)})
asd.Transparency = bbb
asd.Size = aaa
asd.ZOffset = .9
asd.Acceleration = Vector3.new(0, -15, 0)
asd.LockedToPart = false
asd.EmissionDirection = "Back"
asd.Lifetime = NumberRange.new(1, 2)
asd.Rotation = NumberRange.new(-100, 100)
asd.RotSpeed = NumberRange.new(-100, 100)
asd.Speed = NumberRange.new(10)
asd.Enabled = false
asd.VelocitySpread = 999

function getbloody(victim,amount)
	local PART = CreatePart(3, Effects, "Metal", 0, 1, "Really black", "Blood", victim.Size)
	PART.CFrame = victim.CFrame
	local HITPLAYERSOUNDS = {"356551938","264486467"}
	Debris:AddItem(PART,5)
	CreateSound(HITPLAYERSOUNDS[MRANDOM(1, #HITPLAYERSOUNDS)], PART, 1, (math.random(8,12)/10))
	CreateSound(HITPLAYERSOUNDS[MRANDOM(1, #HITPLAYERSOUNDS)], PART, 1, (math.random(8,12)/10))
	CreateSound(HITPLAYERSOUNDS[MRANDOM(1, #HITPLAYERSOUNDS)], PART, 1, (math.random(8,12)/10))
	local prtcl = asd:Clone()
	prtcl.Parent = PART
	prtcl:Emit(amount*10)
end

function Kill2(Char)
	local NewCharacter = IT("Model",Effects)
	NewCharacter.Name = "Ow im ded ;-;"
	for _, c in pairs(Char:GetDescendants()) do
		if c:IsA("BasePart") and c.Transparency == 0 then
			if c.Parent == Char then
				getbloody(c,5)
			end
			local SIZE = c.Size
			c:BreakJoints()
			c.Material = "Neon"
			c.Color = C3(1,0,0)
			c.CanCollide = true
			c.Transparency = 0.3
			if c:FindFirstChildOfClass("SpecialMesh") then
				c:FindFirstChildOfClass("SpecialMesh").TextureId = ""
			end
			if c.Name == "Head" then
				c:ClearAllChildren()
				c.Size = VT(c.Size.Y,c.Size.Y,c.Size.Y)
			end
			if c.ClassName == "MeshPart" then
				c.TextureID = ""
			end
			if c:FindFirstChildOfClass("BodyPosition") then
				c:FindFirstChildOfClass("BodyPosition"):remove()
			end
			if c:FindFirstChildOfClass("ParticleEmitter") then
				c:FindFirstChildOfClass("ParticleEmitter"):remove()
			end
			c.Parent = NewCharacter
			c.Name = "DeadPart"
			c.Velocity = VT(MRANDOM(-45,45),MRANDOM(-45,45),MRANDOM(-45,45))/15
			c.RotVelocity = VT(MRANDOM(-45,45),MRANDOM(-15,85),MRANDOM(-45,45))

		end
	end
	Char:remove()
	Debris:AddItem(NewCharacter,5)
end

function BulletDetection(FROM,TO,BRUTAL)
	local AIMHIT,AIMPOS,NORMAL = CastProperRay(FROM,TO,2000,Character)
	coroutine.resume(coroutine.create(function()
		if AIMHIT ~= nil then
			if AIMHIT.Parent ~= Character then
				if AIMHIT.Parent:FindFirstChildOfClass("Humanoid") or AIMHIT.Parent.Parent:FindFirstChildOfClass("Humanoid") then
					if AIMHIT.Parent:FindFirstChildOfClass("Humanoid") then
						if BRUTAL == true then
							Kill2(AIMHIT.Parent)
						else
							getbloody(AIMHIT,15)
							AIMHIT.Parent:BreakJoints()
							if AIMHIT.Name == "Head" then
								AIMHIT.Name = "HEADSHOT"
								AIMHIT:remove()
							end
						end
					else
						if BRUTAL == true then
							Kill2(AIMHIT.Parent.Parent)
							else
							Banish(AIMHIT.Parent.Parent)
						end
					end
				end
			end
		end
	end))
	SpawnTrail(FROM,AIMPOS)
	return AIMHIT,AIMPOS,NORMAL
end

function BulletDetection2(FROM,TO,BRUTAL)
	local AIMHIT,AIMPOS,NORMAL = CastProperRay(FROM,TO,2000,Character)
	coroutine.resume(coroutine.create(function()
		if AIMHIT ~= nil then
			if AIMHIT.Parent ~= Character then
				if AIMHIT.Parent:FindFirstChildOfClass("Humanoid") or AIMHIT.Parent.Parent:FindFirstChildOfClass("Humanoid") then
					if AIMHIT.Parent:FindFirstChildOfClass("Humanoid") then
						if BRUTAL == true then
							Banish(AIMHIT.Parent)
						else
							getbloody(AIMHIT,15)
							AIMHIT.Parent:BreakJoints()
							if AIMHIT.Name == "Head" then
								AIMHIT.Name = "HEADSHOT"
								AIMHIT:remove()
							end
						end
					else
						if BRUTAL == true then
							Banish(AIMHIT.Parent.Parent)
							else
							Kill2(AIMHIT.Parent.Parent)
						end
					end
				end
			end
		end
	end))
	SpawnTrail(FROM,AIMPOS)
	return AIMHIT,AIMPOS,NORMAL
end

function ApplyAoE2(POSITION,RANGE,ISBANISH)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character then
			local HUM = CHILD:FindFirstChildOfClass("Humanoid")
			if HUM then
				local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
				if TORSO then
					if (TORSO.Position - POSITION).Magnitude <= RANGE then
						if ISBANISH == true then
							Banish(CHILD)
						else
							if ISBANISH == "Gravity" then
								HUM.PlatformStand = true
								if TORSO:FindFirstChild("V3BanishForce"..Player.Name) then
									local grav = Instance.new("BodyPosition",TORSO)
									grav.D = 15
									grav.P = 20000
									grav.maxForce = Vector3.new(math.huge,math.huge,math.huge)
									grav.position = TORSO.Position
									grav.Name = "V3BanishForce"..Player.Name
								else
									TORSO:FindFirstChild("V3BanishForce"..Player.Name).position = TORSO.Position+VT(0,0.3,0)
									TORSO.RotVelocity = VT(MRANDOM(-25,25),MRANDOM(-25,25),MRANDOM(-25,25))
								end
							else
								HUM.PlatformStand = false
							end
						end
					elseif ISBANISH == "Gravity" then
						if TORSO:FindFirstChild("V3BanishForce"..Player.Name) then
							TORSO:FindFirstChild("V3BanishForce"..Player.Name):remove()
							HUM.PlatformStand = false
						end
					end
				end
			end
		end
	end
end

function CORRUPTEDBURNINGBULLETS()
    ATTACK = true
    Rooted = false
    repeat
        local GYRO = IT("BodyGyro",RootPart)
        GYRO.D = 175
        GYRO.P = 20000
        GYRO.MaxTorque = VT(0,40000,0)
        GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
        if COMBO == 1 then
            COMBO = 2
            for i=0, 0, 0.1 / Animation_Speed do
                Swait()
                GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(110), RAD(-15 - 2.5 * SIN(SINE / 12)), RAD(35 + 7.5 * SIN(SINE / 12))) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(-50)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 0, Transparency2 = 1, CFrame = LeftHole.CFrame, MoveToPos = LeftHole.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = LeftHole.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 5, RotationZ = 0, Material = "Neon", Color = C3(255,0,255), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            CreateSound(275326592, LeftHole, 7, 1, false)
            BulletDetection2(LeftHole.Position,Mouse.Hit.p,true)
            for i=0, 0, 0.1 / Animation_Speed do
                Swait()
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(110), RAD(-15 - 2.5 * SIN(SINE / 12)), RAD(35 + 7.5 * SIN(SINE / 12))) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(130), RAD(0), RAD(-50)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
        elseif COMBO == 2 then
            COMBO = 1
            for i=0, 0.1, 0.1 / Animation_Speed do
                Swait()
                GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(50)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(140), RAD(15 + 2.5 * SIN(SINE / 12)), RAD(-35 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = RightHole.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,255), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            CreateSound(275326592, RightHole, 7, 1, false)
            BulletDetection2(RightHole.Position,Mouse.Hit.p,true)
            for i=0, 0.1, 0.1 / Animation_Speed do
                Swait()
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(130), RAD(0), RAD(50)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(140), RAD(15 + 2.5 * SIN(SINE / 12)), RAD(-35 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
        end
        GYRO:remove()
    until KEYHOLD == false
    ATTACK = false
    Rooted = false
end

function CORRUPTEDLETHALBULLETS()
    ATTACK = true
    Rooted = false
    repeat
        local GYRO = IT("BodyGyro",RootPart)
        GYRO.D = 175
        GYRO.P = 20000
        GYRO.MaxTorque = VT(0,40000,0)
        GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
        if COMBO == 1 then
            COMBO = 2
            for i=0, 0, 0.1 / Animation_Speed do
                Swait()
                GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 3  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(110), RAD(-15 - 2.5 * SIN(SINE / 12)), RAD(35 + 7.5 * SIN(SINE / 12))) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(-50)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 0, Transparency2 = 1, CFrame = LeftHole.CFrame, MoveToPos = LeftHole.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = LeftHole.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 5, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            CreateSound(1590205662, LeftHole, 7, 1, false)
            BulletDetection(LeftHole.Position,Mouse.Hit.p,true)
            for i=0, 0, 0.1 / Animation_Speed do
                Swait()
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 3  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(110), RAD(-15 - 2.5 * SIN(SINE / 12)), RAD(35 + 7.5 * SIN(SINE / 12))) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(130), RAD(0), RAD(-50)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
        elseif COMBO == 2 then
            COMBO = 1
            for i=0, 0.1, 0.1 / Animation_Speed do
                Swait()
                GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 3  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(50)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(140), RAD(15 + 2.5 * SIN(SINE / 12)), RAD(-35 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = RightHole.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            CreateSound(1590205662, RightHole, 7, 1, false)
            BulletDetection(RightHole.Position,Mouse.Hit.p,true)
            for i=0, 0.1, 0.1 / Animation_Speed do
                Swait()
                RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 3  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(50)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-50)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(130), RAD(0), RAD(50)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(140), RAD(15 + 2.5 * SIN(SINE / 12)), RAD(-35 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
        end
        GYRO:remove()
    until KEYHOLD == false
    ATTACK = false
    Rooted = false
end

function CORRUPTEDLETHALBULLETS2()
    ATTACK = true
    Rooted = false
    repeat
        local GYRO = IT("BodyGyro",RootPart)
        GYRO.D = 175
        GYRO.P = 20000
        GYRO.MaxTorque = VT(0,40000,0)
        GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
            for i=0, 0.5, 0.5 / Animation_Speed do
                Swait()
                GYRO.cframe = CF(RootPart.Position,Mouse.Hit.p)
		turnto(Mouse.Hit.p)
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(90)), 0.5 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 0.5 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.6, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
            end
		for i=0, 0.3, 5 / Animation_Speed do
			Swait()
			turnto(Mouse.Hit.p)
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(90)), 0.5 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 0.5 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.6, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		end
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = RightHole.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            CreateSound(1590205662, RightArm, 7, 1, false)
            BulletDetection(RightHole.Position,Mouse.Hit.p,true)
		for i=0, 0.4, 5 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(90)), 0.5 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 0.25 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(15), RAD(90)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.6, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		end
        GYRO:remove()
    until KEYHOLD == false
    ATTACK = false
    Rooted = false
end


function Corrupted_Burn()
	ATTACK = true
	Rooted = true
	for i=0, 1, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(15 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, -0.5) * ANGLES(RAD(0), RAD(0), RAD(-85)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.15, -0.5) * ANGLES(RAD(-15), RAD(0), RAD(85)) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
	end	
	coroutine.resume(coroutine.create(function()
		local POS = Mouse.Hit.p
		local RAY = CreatePart(3, Effects, "Neon", 0, 0, "Really red", "Strike", VT(0,2000,0))
		MakeForm(RAY,"Cyl")
		local SPHERE = CreatePart(3, Effects, "Neon", 0, 0, "Hot pink", "Strike", VT(0,0,0))
		MakeForm(SPHERE,"Ball")
		local SHIELD = CreatePart(3, Effects, "Neon", 0, 0.5, "Really black", "Strike", VT(0,0,0))
		MakeForm(SHIELD,"Ball")
		SHIELD.CFrame = CF(POS)
		RAY.CFrame = CF(POS)
		SPHERE.CFrame = CF(POS)
		CreateSound(440145570, SPHERE, 10, 0.8, false)
		CreateSound(415700134, SPHERE, 10, 0.8, false)
		for i = 1, 200 do
			Swait()
			WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0), Size2 = VT(SPHERE.Size.X*1.2,5+(i),SPHERE.Size.X*1.2), Transparency = 0, Transparency2 = 1, CFrame = SPHERE.CFrame*ANGLES(RAD(0), RAD(i), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = i, RotationZ = 0, Material = "Neon", Color = C3(0,255,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
			RAY.Size = RAY.Size + VT(0.05,0,0.05)
			SPHERE.Size = SPHERE.Size + VT(5,5,5)
			SHIELD.Size = SPHERE.Size + VT(10,10,10)
			ApplyAoE2(SPHERE.Position,SPHERE.Size.X/5,true)
		end	
		for i = 1, 45 do
			Swait()
			RAY.Transparency = RAY.Transparency + 1/45
			SPHERE.Transparency = RAY.Transparency 
			SHIELD.Transparency = SPHERE.Transparency + 1/45
		end
		RAY:remove()
		SHIELD:remove()
		SPHERE:remove()
	end))
	for i=0, 1, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0  + 0.25 * COS(SINE / 12)) * ANGLES(RAD(-35), RAD(0), RAD(0)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(-15 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, -0.15) * ANGLES(RAD(65), RAD(-45), RAD(85)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, -0.15) * ANGLES(RAD(65), RAD(45), RAD(-85)) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	ATTACK = false
	Rooted = false
end

local Weapon = IT("Model")
Weapon.Name = "Adds"

local Eon = CreatePart(3, Weapon, "Neon", 0, 0, "Lime green", "Eon", VT(0,0,0),false)
CreateWeldOrSnapOrMotor("Weld", Torso, Torso, Eon, CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), CF(0, 0, 0))

function MagicSpheres(SIZE,WAIT,CFRAME,COLOR,GROW)
	local wave = CreatePart(3, Effects, "Neon", 0, 0, BRICKC(COLOR), "Effect", VT(1,1,1), true)
	local mesh = IT("SpecialMesh",wave)
	mesh.MeshType = "Sphere"
	mesh.Scale = SIZE
	mesh.Offset = VT(0,0,0)
	wave.CFrame = CFRAME
	coroutine.resume(coroutine.create(function(PART)
		for i = 1, WAIT do
			Swait()
			mesh.Scale = mesh.Scale + GROW
			wave.Transparency = wave.Transparency + (1/WAIT)
			if wave.Transparency > 0.99 then
				wave:remove()
			end
		end
	end))
end

function Warp()
	ATTACK = true
	Rooted = true
	UNANCHOR = false
	RootPart.Anchored = true
	MagicSpheres(VT(0,0,0),15,Eon.CFrame,"Really red",VT(2,2,2))
	MagicSpheres(VT(0,0,0),15,Eon.CFrame,"Royal Purple",VT(2,2,2))
	for i=0, 0.5, 0.1 / Animation_Speed do
		Swait()
				WACKYEFFECT({
					Time = 5,
					EffectType = "Round Slash",
					Size = VT(0, 0, 0),
					Size2 = VT(0.01, 0, 0.01),
					Transparency = 0.5,
					Transparency2 = 1,
					CFrame = CF(Eon.Position) * ANGLES(RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360))),
					MoveToPos = nil,
					RotationX = MRANDOM(-50, 50) / 10,
					RotationY = MRANDOM(-50, 50) / 10,
					RotationZ = MRANDOM(-50, 50) / 10,
					Material = "Neon",
					Color = C3(1, 0, 0),
					SoundID = nil,
					SoundPitch = nil,
					SoundVolume = nil
				})
		MagicSpheres(VT(0,0.2,0),15,CF(RootPart.Position-VT(0,3,0)),"Really black",VT(0.5,0,0.5))
					RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, -0.1, -0.1 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(15), RAD(0), RAD(0)), 0.15 / Animation_Speed)
					Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 0.15 / Animation_Speed)
					RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.35, 0) * ANGLES(RAD(15), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
					LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.35, 0) * ANGLES(RAD(15), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
					RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(20), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
					LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(5), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
	end
	for i = 1, 10 do
		Swait()
		MagicSpheres(VT(0,0.2,0),15,CF(RootPart.Position-VT(0,3,0)),"Really black",VT(0.5,0,0.5))
	end
	for i=0, 1, 0.1 / Animation_Speed do
		Swait()
				WACKYEFFECT({
					Time = 5,
					EffectType = "Round Slash",
					Size = VT(0, 0, 0),
					Size2 = VT(0.01, 0, 0.01),
					Transparency = 0.5,
					Transparency2 = 1,
					CFrame = CF(Eon.Position) * ANGLES(RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360))),
					MoveToPos = nil,
					RotationX = MRANDOM(-50, 50) / 10,
					RotationY = MRANDOM(-50, 50) / 10,
					RotationZ = MRANDOM(-50, 50) / 10,
					Material = "Neon",
					Color = C3(1, 0, 0),
					SoundID = nil,
					SoundPitch = nil,
					SoundVolume = nil
				})
		MagicSpheres(VT(0,0.2,0),15,CF(RootPart.Position-VT(0,3,0)),"Crimson",VT(0.5,0,0.5))
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -25) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.5 / Animation_Speed)
					Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 0.15 / Animation_Speed)
					RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.35, 0) * ANGLES(RAD(15), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
					LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.35, 0) * ANGLES(RAD(15), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
					RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(20), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
					LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(5), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
	end
	local ORIGIN = RootPart.Position
	RootPart.CFrame = CF(Mouse.Hit.p+VT(0,3,0),ORIGIN)
	for i=0, 1, 0.1 / Animation_Speed do
		Swait()
				WACKYEFFECT({
					Time = 5,
					EffectType = "Round Slash",
					Size = VT(0, 0, 0),
					Size2 = VT(0.01, 0, 0.01),
					Transparency = 0.5,
					Transparency2 = 1,
					CFrame = CF(Eon.Position) * ANGLES(RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360))),
					MoveToPos = nil,
					RotationX = MRANDOM(-50, 50) / 10,
					RotationY = MRANDOM(-50, 50) / 10,
					RotationZ = MRANDOM(-50, 50) / 10,
					Material = "Neon",
					Color = C3(1, 0, 0),
					SoundID = nil,
					SoundPitch = nil,
					SoundVolume = nil
				})
		MagicSpheres(VT(0,0.2,0),15,CF(RootPart.Position-VT(0,3,0)),"Really black",VT(0.5,0,0.5))
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.5 / Animation_Speed)
					Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 0.15 / Animation_Speed)
					RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.35, 0) * ANGLES(RAD(15), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
					LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.35, 0) * ANGLES(RAD(15), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
					RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(20), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
					LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(5), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
	end
	UNANCHOR = true
	RootPart.Anchored = false
	for i = 1, 10 do
		Swait()
		MagicSpheres(VT(0,0.2,0),15,CF(RootPart.Position-VT(0,3,0)),"Hot pink",VT(0.5,0,0.5))
	end
	ATTACK = false
	Rooted = false
end

function Neckless()
	local TARGET = Mouse.Target
	if TARGET ~= nil then
		if TARGET.Parent:FindFirstChildOfClass("Humanoid") then
			local HUM = TARGET.Parent:FindFirstChildOfClass("Humanoid")
			local ROOT = TARGET.Parent:FindFirstChild("HumanoidRootPart") or TARGET.Parent:FindFirstChild("Torso") or TARGET.Parent:FindFirstChild("UpperTorso")
			if ROOT and HUM.Health > 0 then
				local FOE = Mouse.Target.Parent
				local HEAD = FOE:FindFirstChild("Head")
				if HEAD then
					ATTACK = true
					Rooted = false
					CharacterFade(C3(0,0,0),150)
					RootPart.CFrame = ROOT.CFrame*CF(0,0,2)
					for _, c in pairs(FOE:GetChildren()) do
						if c.ClassName == "Part" then
							c.Anchored = true
						end
					end
					CreateSound(235097614, Torso, 2, 3, false)
					for i=0, 0.5, 0.1 / Animation_Speed do
						Swait()
						RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
						Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * COS(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
						RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.1, 0.5, -0.6) * ANGLES(RAD(130), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
						LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.1, 0.5, -0.6) * ANGLES(RAD(130), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
						RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
					end
					if ROOT.Name == "HumanoidRootPart" then
						ROOT:remove()
					end
					FOE:BreakJoints()
					ApplyDamage(HUM,0,true)
					CreateSound(363808674, HEAD, 5, 1, false)
					ROOT.Anchored = false
					for i=0, 0.5, 0.1 / Animation_Speed do
						Swait()
						RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
						Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * COS(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
						RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.1, 0.65, -1.5) * ANGLES(RAD(130), RAD(0), RAD(-35)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
						LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.1, 0.5, 0) * ANGLES(RAD(130), RAD(0), RAD(0)) * LEFTSHOULDERC0, 2 / Animation_Speed)
						RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
					end
					for _, c in pairs(FOE:GetChildren()) do
						if c.ClassName == "Part" then
							c.Anchored = false
						end
					end
					ATTACK = false
					Rooted = false
				end
			end
		end
	end
end
function BraveSpeed()
	CreateSound(235097614, Torso, 0.2, 3, false)
	for i = 1, 7 do
		CharacterFade(C3(0,0,0),25+(i*10))
		RootPart.CFrame = RootPart.CFrame*CF(0,0,-4)
	end
end
function Slashed()
	local TARGET = Mouse.Target
	if TARGET ~= nil then
		if TARGET.Parent:FindFirstChildOfClass("Humanoid") then
			local HUM = TARGET.Parent:FindFirstChildOfClass("Humanoid")
			local ROOT = TARGET.Parent:FindFirstChild("Torso") or TARGET.Parent:FindFirstChild("UpperTorso")
			if ROOT and HUM.Health > 0 then
				local FOE = Mouse.Target.Parent
				ATTACK = true
				coroutine.resume(coroutine.create(function()
					repeat
						Swait()
						RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0.05, -0.05 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(15), RAD(0), RAD(0)), 1 / Animation_Speed)
						Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(MRANDOM(-5,5) - 2.5 * COS(SINE / 12)), RAD(MRANDOM(-5,5)), RAD(0)), 1 / Animation_Speed)
						RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.45, 0.5, -0.1) * ANGLES(RAD(50), RAD(0), RAD(-30)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
						RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(15), RAD(85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(15), RAD(-85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
					until ATTACK == false
				end))
				for i=0, 0.2, 0.1 / Animation_Speed do
					Swait()
					LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.15, -0.85) * ANGLES(RAD(35), RAD(0), RAD(90)) * ANGLES(RAD(0), RAD(-90), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
				end
				for i=0, 1.2, 0.1 / Animation_Speed do
					Swait()
					LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.25, -0.5) * ANGLES(RAD(90), RAD(0), RAD(0)) * LEFTSHOULDERC0, 0.2 / Animation_Speed)
				end
				CreateSound(971125740, LeftArm, 5, 1, false)
				for i=0, 0.1, 0.1 / Animation_Speed do
					Swait()
					WACKYEFFECT({Time = 25, EffectType = "Box", Size = VT(1,2,1), Size2 = VT(1,2,1), Transparency = 0.2, Transparency2 = 1, CFrame = LeftArm.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
					LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.1, 0.15, -0.85) * ANGLES(RAD(35), RAD(0), RAD(90)) * ANGLES(RAD(0), RAD(-90), RAD(0)) * LEFTSHOULDERC0, 2 / Animation_Speed)
				end
				ROOT.CFrame = ROOT.CFrame * ANGLES(RAD(-15), RAD(0), RAD(15))
				WACKYEFFECT({Time = 30, EffectType = "Sphere", Size = VT(1,0.1,1), Size2 = VT(6,0,6)*ROOT.Size.Z, Transparency = 0, Transparency2 = 1, CFrame = ROOT.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = 971126018, SoundPitch = 1.5, SoundVolume = 4})
				WACKYEFFECT({Time = 30, EffectType = "Sphere", Size = VT(1,0.1,1), Size2 = VT(6,0,6)*ROOT.Size.Z, Transparency = 0, Transparency2 = 1, CFrame = Torso.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = 971126018, SoundPitch = 1.5, SoundVolume = 4})
				coroutine.resume(coroutine.create(function()
					for i = 1, 5 do
						Chunks(ROOT)
					end
					local FAKEROOT1 = CreatePart(3, FOE, ROOT.Material, 0, 0, ROOT.BrickColor, "SlicedTorso", VT(ROOT.Size.X,ROOT.Size.Y/2,ROOT.Size.Z),false)
					FAKEROOT1.CanCollide = true
					local FAKEROOT2 = CreatePart(3, FOE, ROOT.Material, 0, 0, ROOT.BrickColor, "SlicedTorso", VT(ROOT.Size.X,ROOT.Size.Y/2,ROOT.Size.Z),false)
					FAKEROOT2.CanCollide = true
					FAKEROOT1.CFrame = ROOT.CFrame*CF(0,ROOT.Size.Y/4,0)
					FAKEROOT2.CFrame = ROOT.CFrame*CF(0,-ROOT.Size.Y/4,0)
					ROOT:Remove()
					ApplyDamage(HUM,0,true)
				end))
				for i=0, 0.4, 0.1 / Animation_Speed do
					Swait()
					LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.1, 0.15, -0.85) * ANGLES(RAD(35), RAD(0), RAD(90)) * ANGLES(RAD(0), RAD(-90), RAD(0)) * LEFTSHOULDERC0, 2 / Animation_Speed)
				end
				ATTACK = false
			end
		end
	end
end
function Dirtface()
	local TARGET = Mouse.Target
	if TARGET ~= nil then
		if TARGET.Parent:FindFirstChildOfClass("Humanoid") then
			local HUM = TARGET.Parent:FindFirstChildOfClass("Humanoid")
			local ROOT = TARGET.Parent:FindFirstChild("HumanoidRootPart") or TARGET.Parent:FindFirstChild("Torso") or TARGET.Parent:FindFirstChild("UpperTorso")
			if ROOT and HUM.Health > 0 then
				local FOE = Mouse.Target.Parent
				local HEAD = FOE:FindFirstChild("Head")
				local HITFLOOR = Raycast(ROOT.Position, (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 4*ROOT.Size.Z, FOE)
				if HEAD and HITFLOOR then
					ATTACK = true
					Rooted = true
					CharacterFade(C3(0,0,0),150)
					RootPart.CFrame = ROOT.CFrame*CF(0,0,2)
					ROOT.Anchored = true
					CreateSound(235097614, Torso, 2, 3, false)
					for i=0, 0.4, 0.1 / Animation_Speed do
						Swait()
						RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-25)), 1 / Animation_Speed)
						Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * COS(SINE / 12)), RAD(0), RAD(25)), 1 / Animation_Speed)
						RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(140), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
						LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
						RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-65), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
					end
					for i=0, 0.1, 0.1 / Animation_Speed do
						Swait()
						RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(25)), 1 / Animation_Speed)
						Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * COS(SINE / 12)), RAD(0), RAD(-25)), 1 / Animation_Speed)
						RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(140), RAD(0), RAD(25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
						LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
						RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
					end
					ROOT.Anchored = false
					UNANCHOR = false
					local DEAD = false
					local CFRAME = RootPart.CFrame
					CreateSound(260411131, Torso, 2, 3, false)
					coroutine.resume(coroutine.create(function()
						repeat
							Swait()
							RootPart.CFrame = CFRAME
							HEAD.CFrame = RightArm.CFrame*CF(0,-(1+HEAD.Size.Z/2),0) * ANGLES(RAD(-90), RAD(0), RAD(0))
							HEAD.Velocity = VT(0,0,0)
							HUM.PlatformStand = true
						until DEAD == true
					end))
					for i=0, 0.2, 0.1 / Animation_Speed do
						Swait()
						RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(25)), 1 / Animation_Speed)
						Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * COS(SINE / 12)), RAD(0), RAD(-25)), 1 / Animation_Speed)
						RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.45, 0.5, -0.3) * ANGLES(RAD(140), RAD(0), RAD(-15)) * RIGHTSHOULDERC0, 0.3 / Animation_Speed)
						LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
						RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(65), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
					end
					for i=0, 1, 0.1 / Animation_Speed do
						Swait()
						RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-15)), 1 / Animation_Speed)
						Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(35 - 2.5 * COS(SINE / 12)), RAD(0), RAD(15)), 1 / Animation_Speed)
						RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.45, 1, 0) * ANGLES(RAD(60), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
						LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
						RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-70), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
					end
					local ERUPT = function()
						local HITFLOOR,HITPOS = Raycast(HEAD.CFrame*CF(0,0.2,0).p+VT(0,0.2,0), (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 4*ROOT.Size.X, FOE)
						if HITFLOOR then
							for i = 1, 5 do
								CreateFlyingDebree(HITFLOOR,CF(HITPOS),MRANDOM(1,2),VT(MRANDOM(10,60)/20,MRANDOM(10,60)/20,MRANDOM(10,60)/20),5,MRANDOM(45,85))
							end
							Chunks(HEAD)
							WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(1,2,1), Size2 = VT(15,0,15), Transparency = 0, Transparency2 = 1, CFrame = CF(HITPOS) * ANGLES(RAD(0), RAD(MRANDOM(0,360)), RAD(0)) , MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1,1,1), SoundID = 765590102, SoundPitch = MRANDOM(8,12)/10, SoundVolume = 4})
						end
					end
					local ATE = false
					local DEPTH = 1
					coroutine.resume(coroutine.create(function()
						repeat
							Swait()
							RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.75 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(45), RAD(0), RAD(15)), 1 / Animation_Speed)
							Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(35 + MRANDOM(-5,5) - 2.5 * COS(SINE / 12)), RAD(MRANDOM(-5,5)), RAD(-15)), 1 / Animation_Speed)
							RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.45, 1-DEPTH/5, -DEPTH/8) * ANGLES(RAD(60 + MRANDOM(-5,5)), RAD(0), RAD(25 + MRANDOM(-5,5))) * ANGLES(RAD(0), RAD(80), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
							LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(5), RAD(0), RAD(5)) * LEFTSHOULDERC0, 1 / Animation_Speed)
							RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.25 - 0.05 * COS(SINE / 12), -0.5) * ANGLES(RAD(40), RAD(70), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(0)), 1 / Animation_Speed)
							LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.3) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-1), RAD(0), RAD(0)), 1 / Animation_Speed)
						until ATE == true
					end))
					wait()
					ERUPT()
					wait(2)
					ERUPT()
					DEPTH = 2
					wait(2)
					ERUPT()
					DEPTH = 2.5
					wait(3)
					ERUPT()
					ERUPT()
					HEAD:remove()
					DEAD = true
					ApplyDamage(HUM,0,true)
					wait(0.2)
					ATE = true
					UNANCHOR = true
					ATTACK = false
					Rooted = false
				end
			end
		end
	end
end


function ApplyAoE5(POSITION, RANGE, MINDMG, MAXDMG, FLING, EBANISH)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character then
			local HUM = CHILD:FindFirstChildOfClass("Humanoid")
			if HUM then
				local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
				if TORSO and RANGE >= (TORSO.Position - POSITION).Magnitude then
					if EBANISH == true then
						Banish(CHILD)
					else
						local DMG = MRANDOM(MINDMG, MAXDMG)
						ApplyDamage(HUM, DMG, TORSO)
					end
					if FLING > 0 then
						for _, c in pairs(CHILD:GetChildren()) do
							if c:IsA("BasePart") then
								local bv = Instance.new("BodyVelocity")
								bv.maxForce = Vector3.new(1000000000, 1000000000, 1000000000)
								bv.velocity = CF(POSITION, TORSO.Position).lookVector * FLING
								bv.Parent = c
								Debris:AddItem(bv, 0.05)
							end
						end
					end
				end
			end
		end
	end
end

local A = IT("Attachment",RightBarrel)
A.Position = VT(0,-2.5,0)
local B = IT("Attachment",RightBarrel)
B.Position = VT(0,2.5,0)
local Trail = IT("Trail",RightBarrel)
Trail.Attachment0 = A
Trail.Attachment1 = B
Trail.Lifetime = 0.2
Trail.Color = ColorSequence.new(BRICKC"Crimson".Color)
Trail.Transparency = NumberSequence.new(0, 1)
Trail.Enabled = false

function Execute()
	ATTACK = true
	Rooted = false
	local Part = CreatePart(3, Character, "Neon", 0, 0, "Crimson", "Part", VT(0,1,4),false)
	Part.Color = C3(0,0,0)
	MakeForm(Part,"Wedge")
	Part.CanCollide = true
	CreateWeldOrSnapOrMotor("Weld", Handle, RightBarrel, Part, CF(0, 0, 0) * ANGLES(RAD(90), RAD(0), RAD(135)) *CF(0, 0.5, 0), CF(0, 0, 0))
	for i=0, 1, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.2 + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-50)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(50)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(125), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(140 - 12 * SIN(SINE / 12)), RAD(15 + 2.5 * SIN(SINE / 12)), RAD(-35 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	Trail.Enabled = true
	CreateSound(541909867, RightBarrel, 7, 1, false)
	local TOCH = Part.Touched:Connect(function(hit)
		if hit.Parent:FindFirstChildOfClass("Humanoid") and hit.Parent ~= Character then
			Banish(hit.Parent)
		end
	end)
	for i=0, 0.35, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.2 + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(50)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-45)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.15, 0.25, -0.3) * ANGLES(RAD(50), RAD(0), RAD(-35)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(140 - 12 * SIN(SINE / 12)), RAD(15 + 2.5 * SIN(SINE / 12)), RAD(-35 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	TOCH:disconnect()
	Trail.Enabled = false
	for i=0, 0.35, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.2 + 0.25 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(60)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-55)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.15, 0.25, -0.3) * ANGLES(RAD(50), RAD(0), RAD(-45)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25, 0.35 + 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(140 - 12 * SIN(SINE / 12)), RAD(15 + 2.5 * SIN(SINE / 12)), RAD(-35 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(-35-2.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	Part:remove()
	ATTACK = false
	Rooted = false
end

local DECAL = IT("Decal")
function MakeRing()
	local RING = CreatePart(3, Effects, "Neon", 0, 1, BRICKC("Pearl"), "MagicRing", VT(0, 0, 0), true)
	local MSH = IT("BlockMesh", RING)
	local TOP = DECAL:Clone()
	local BOTTOM = DECAL:Clone()
	TOP.Parent = RING
	BOTTOM.Parent = RING
	TOP.Face = "Top"
	BOTTOM.Face = "Bottom"
	TOP.Texture = "http://www.roblox.com/asset/?id=647661410"
	BOTTOM.Texture = "http://www.roblox.com/asset/?id=647661410"
	local function REMOVE()
		coroutine.resume(coroutine.create(function()
			local SIZE = MSH.Scale.X
			for i = 1, 35 do
				Swait()
				MSH.Scale = MSH.Scale - VT(SIZE, 0, SIZE) / 60
				TOP.Transparency = TOP.Transparency + 0.02857142857142857
				BOTTOM.Transparency = BOTTOM.Transparency + 0.02857142857142857
				RING.CFrame = RING.CFrame * ANGLES(RAD(0), RAD(-5), RAD(0))
			end
			RING:remove()
		end))
	end
	return RING, MSH, REMOVE
end

function ApplyAoE(POSITION,RANGE,ISBANISH)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character then
			local HUM = CHILD:FindFirstChildOfClass("Humanoid")
			if HUM then
				local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
				if TORSO then
					if (TORSO.Position - POSITION).Magnitude <= RANGE then
						if ISBANISH == true then
							Banish(CHILD)
						else
							if ISBANISH == "Gravity" then
								HUM.PlatformStand = true
								if TORSO:FindFirstChild("V3BanishForce"..Player.Name) then
									local grav = Instance.new("BodyPosition",TORSO)
									grav.D = 15
									grav.P = 20000
									grav.maxForce = Vector3.new(math.huge,math.huge,math.huge)
									grav.position = TORSO.Position
									grav.Name = "V3BanishForce"..Player.Name
								else
									TORSO:FindFirstChild("V3BanishForce"..Player.Name).position = TORSO.Position+VT(0,0.3,0)
									TORSO.RotVelocity = VT(MRANDOM(-25,25),MRANDOM(-25,25),MRANDOM(-25,25))
								end
							else
								HUM.PlatformStand = false
							end
						end
					elseif ISBANISH == "Gravity" then
						if TORSO:FindFirstChild("V3BanishForce"..Player.Name) then
							TORSO:FindFirstChild("V3BanishForce"..Player.Name):remove()
							HUM.PlatformStand = false
						end
					end
				end
			end
		end
	end
end

function Smite()
	local RING, MESH, DELET = MakeRing()
	local POS = Mouse.Hit.p
	RING.CFrame = CF(Mouse.Hit.p + VT(MRANDOM(-25, 25), 200, MRANDOM(-25, 25)), Mouse.Hit.p) * ANGLES(RAD(90), RAD(0), RAD(0))
	for i = 1, 45 do
		Swait()
		MESH.Scale = MESH.Scale + VT(12, 0, 12)
		RING.CFrame = RING.CFrame * ANGLES(RAD(0), RAD(5), RAD(0))
	end
	local HITFLOOR, HITPOS = Raycast(RING.Position, CF(RING.Position, RING.CFrame * CF(0, -1, 0).p).lookVector, 500, Character)
	if HITFLOOR then
		local BEAM = CreatePart(3, Effects, "Neon", 0, 0, BRICKC("Really black"), "Beam", VT(0, 0, 0), true)
		MakeForm(BEAM, "Cyl")
		local DIST = (RING.Position - HITPOS).Magnitude
		BEAM.Size = VT(0, DIST, 0)
		BEAM.CFrame = CF(RING.Position, HITPOS) * CF(0, 0, -DIST / 2) * ANGLES(RAD(90), RAD(0), RAD(0))
		for i = 1, 5 do
			WACKYEFFECT({
				EffectType = "Wave",
				Size = VT(25, 0, 25),
				Size2 = VT(40, 0, 40) + VT(i * 6, i / 5, i * 6),
				Transparency = 0,
				Transparency2 = 1,
				CFrame = CF(HITPOS) * ANGLES(RAD(0), RAD(72 * i), RAD(0)),
				MoveToPos = nil,
				RotationX = 0,
				RotationY = 3,
				RotationZ = 0,
				Material = "Neon",
				Color = C3(1, 0, 0),
				SoundID = nil,
				SoundPitch = nil,
				SoundVolume = nil
			})
			WACKYEFFECT({
				EffectType = "Round Slash",
				Size = VT(3, 0, 3) / 13,
				Size2 = (VT(3, 0, 3) + VT(i, 0, i)) / 5,
				Transparency = 0,
				Transparency2 = 1,
				CFrame = CF(HITPOS) * ANGLES(RAD(0), RAD(MRANDOM(0, 360)), RAD(0)) * ANGLES(RAD(MRANDOM(-35, 35)), RAD(0), RAD(MRANDOM(-35, 35))),
				MoveToPos = nil,
				RotationX = 0,
				RotationY = 0,
				RotationZ = 0,
				Material = "Neon",
			    Color = C3(1, 0, 1),
				SoundID = nil,
				SoundPitch = nil,
				SoundVolume = nil
			})
			WACKYEFFECT({
				Time = 35,
				EffectType = "Sphere",
				Size = VT(22, 22, 22),
				Size2 = VT(45, 45, 45) + VT(i * 5, i * 5, i * 5),
				Transparency = 0,
				Transparency2 = 1,
				CFrame = CF(HITPOS),
				MoveToPos = nil,
				RotationX = 0,
				RotationY = 0,
				RotationZ = 0,
				Material = "Neon",
				Color = C3(0, .7, 0),
				SoundID = 459523898,
				SoundPitch = MRANDOM(9, 12) / 10,
				SoundVolume = 10
			})
		end
		ApplyAoE(HITPOS, 50, true)
		for i = 1, 25 do
			Swait()
			BEAM.Size = BEAM.Size + VT(0.15, 0, 0.15)
			BEAM.Transparency = BEAM.Transparency + 0.04
		end
		BEAM:remove()
	end
	wait(0.2)
	DELET()
end

function CorruptedBurningBeam()
	ATTACK = true
	Rooted = false
	chatfunc("Why Dont You Just Leave My Sights Already")
	local GYRO = IT("BodyGyro", RootPart)
	GYRO.D = 20
	GYRO.P = 4000
	GYRO.MaxTorque = VT(0, 40000, 0)
	local RING, MESH, DELET = MakeRing()
	local POS = RootPart.Position + VT(0, 25, 0)
	RING.CFrame = CF(POS, Mouse.Hit.p) * ANGLES(RAD(90), RAD(0), RAD(0))
	CreateSound(459523787, RING, 8, 1, false)
	local BLASTS = {468991944, 468991990}
	coroutine.resume(coroutine.create(function()
		local E = 0
		repeat
			E = E + 5
			GYRO.CFrame = CF(RootPart.Position, Mouse.Hit.p)
			Swait()
			RING.CFrame = CF(POS, Mouse.Hit.p) * ANGLES(RAD(90), RAD(E), RAD(0))
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0 + 0.45 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(90)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0 + 4.5 * SIN(SINE / 12)), RAD(0), RAD(-90)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5 + 0.25 * COS(SINE / 12), 0) * ANGLES(RAD(0), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5 + 0.25 * COS(SINE / 12), 0) * ANGLES(RAD(0 - 7.5 * SIN(SINE / 12)), RAD(0 + 7.5 * SIN(SINE / 12)), RAD(-12 - 7.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 + 0.15 * COS(SINE / 12), -0.01) * ANGLES(RAD(-7.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5 + 0.25 * COS(SINE / 12), -0.5) * ANGLES(RAD(-7.5 * SIN(SINE / 12)), RAD(-90), RAD(0)) * ANGLES(RAD(-8 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		until ATTACK == false
		GYRO:remove()
		DELET()
	end))
	for i = 1, 50 do
		Swait()
		MESH.Scale = MESH.Scale + VT(22, 0, 22)
	end
	for i = 1, 25 do
		Swait()
		WACKYEFFECT({
			Time = 15,
			EffectType = "Sphere",
			Size = VT(4, 4, 4),
			Size2 = VT(0, 0, 0),
			Transparency = 1,
			Transparency2 = 0,
			CFrame = CF(RING.Position) * ANGLES(RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360))) * CF(0, 0, 35),
			MoveToPos = RING.Position,
			RotationX = 0,
			RotationY = 0,
			RotationZ = 0,
			Material = "Neon",
			Color = C3(1, 0, 0),
			SoundID = nil,
			SoundPitch = nil,
			SoundVolume = nil
		})
	end
	local LOOP = 0
	local BEAM = CreatePart(3, Effects, "Neon", 0, 0, BRICKC("Dark indigo"), "Beam", VT(0, 0, 0), true)
	MakeForm(BEAM, "Cyl")
	CreateSound(BLASTS[MRANDOM(1, #BLASTS)], RING, 5, MRANDOM(9, 11) / 10, false)
	repeat
		local DISTANCE = (RING.Position - Mouse.Hit.p).Magnitude
		if DISTANCE < 2000 then
			BEAM.Size = VT(10 + 2 * COS(SINE / 4), DISTANCE, 10 + 2 * COS(SINE / 4))
			BEAM.CFrame = CF(RING.Position, Mouse.Hit.p) * CF(0, 0, -DISTANCE / 2) * ANGLES(RAD(90), RAD(0), RAD(0))
			ApplyAoE(Mouse.Hit.p, 35, true)
			WACKYEFFECT({
				Time = 35,
				EffectType = "Sphere",
				Size = VT(10 + 2 * COS(SINE / 4), 10 + 2 * COS(SINE / 4), 10 + 2 * COS(SINE / 4)) * 2,
				Size2 = VT(5, 75, 5),
				Transparency = 0,
				Transparency2 = 1,
				CFrame = CF(Mouse.Hit.p) * ANGLES(RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360))),
				MoveToPos = nil,
				RotationX = 0,
				RotationY = 0,
				RotationZ = 0,
				Material = "Neon",
				Color = C3(0, 0, 0),
				SoundID = nil,
				SoundPitch = MRANDOM(9, 12) / 10,
				SoundVolume = 10
			})
			Swait()
			LOOP = LOOP + 1
		end
	until KEYHOLD == false and LOOP >= 35 or DISTANCE >= 2000
	coroutine.resume(coroutine.create(function()
		for i = 1, 15 do
			Swait()
			BEAM.Size = BEAM.Size - VT(0.8, 0, 0.8)
			BEAM.Transparency = BEAM.Transparency + 0.06666666666666667
		end
		BEAM:remove()
	end))
	ATTACK = false
	Rooted = false
end

function nope()
    ATTACK = true
    Rooted = false
      Chatter("Why you use this script for abuse?")
    for i=0, 5, 0.1 / Animation_Speed do
        Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 - 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.5 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(15), RAD(0), RAD(20 * COS(SINE / 7))), 0.5 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1, 0.5 - 0.1 * COS(SINE / 12), -0.5) * ANGLES(RAD(0), RAD(0), RAD(-100)) * ANGLES(RAD(20), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1, 0.3 - 0.1 * COS(SINE / 12), -0.35) *  ANGLES(RAD(0), RAD(0), RAD(70)) * ANGLES(RAD(20), RAD(0), RAD(0)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 + 0.05 * COS(SINE / 12),-0.01) * ANGLES(RAD(0), RAD(85), RAD(0)) * ANGLES(RAD(-6), RAD(0), RAD(0)), 0.5 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 + 0.05 * COS(SINE / 12),-0.01) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-6), RAD(0), RAD(0)), 0.5 / Animation_Speed)
    end
    ATTACK = false
    Rooted = false
end


function PlanetaryDevastation()
	ATTACK = true
	Rooted = true
	chatfunc("Burn In My Special Hell")
	local SIZE = 1
	local GYRO = IT("BodyGyro", RootPart)
	GYRO.D = 20
	GYRO.P = 4000
	GYRO.MaxTorque = VT(0, 40000, 0)
	local RING, MESH, DELET = MakeRing()
	local HITFLOOR, HITPOS = Raycast(RootPart.Position, CF(RootPart.Position, RootPart.Position + VT(0, -1, 0)).lookVector, 15, Character)
	RING.CFrame = CF(HITPOS)
	for i = 0, 0.6, 0.1 / Animation_Speed do
		GYRO.CFrame = CF(RootPart.Position, Mouse.Hit.p)
		Swait()
		MESH.Scale = MESH.Scale + VT(53, 0, 53)
		RING.CFrame = RING.CFrame * ANGLES(RAD(0), RAD(5), RAD(0))
		WACKYEFFECT({TIME = 15, EffectType = "Block", Size = VT(3,3,3)/3, Size2 = VT(1,1,1)/3, Transparency = 0.5, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1.3,0), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(1,0,0), SoundID = nil, SoundPitch = 1, SoundVolume = 5})
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(-25 - 4 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.6, 0.75, -0.5) * ANGLES(RAD(0), RAD(-45), RAD(12)) * ANGLES(RAD(125 - 2.5 * COS(SINE / 12) + 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 2.5 / Animation_Speed)
	end
	local BLASTS = {468991944, 468991990}
	coroutine.resume(coroutine.create(function()
		local CFRAME = RootPart.CFrame
		for i = 1, 100 do
			CFRAME = CFRAME * CF(0, 0, -35)
			do
				local HITFLOOR, HITPOS = Raycast(CFRAME.p, CF(RootPart.Position, RootPart.Position + VT(0, -1, 0)).lookVector, 15, Character)
				if HITFLOOR then
					Swait()
					do
						local OFFSET = CFRAME * CF(MRANDOM(-25, 25), 0, 0)
						coroutine.resume(coroutine.create(function()
							local RING, MESH, DELET = MakeRing()
							RING.CFrame = CF(OFFSET.p.X, HITPOS.Y, OFFSET.p.Z)
							for i = 1, 25 do
								Swait()
								MESH.Scale = MESH.Scale + VT(42, 0, 42)
								RING.CFrame = RING.CFrame * ANGLES(RAD(0), RAD(5), RAD(0))
							end
							ApplyAoE(RING.Position, 65, true)
							local TURN = ANGLES(RAD(0), RAD(MRANDOM(0, 360)), RAD(0)) * ANGLES(RAD(MRANDOM(0, 25)), RAD(0), RAD(0))
							WACKYEFFECT({
								Time = 25,
								EffectType = "Sphere",
								Size = VT(22, 22, 22),
								Size2 = VT(85, 85, 85),
								Transparency = 0,
								Transparency2 = 1,
								CFrame = CF(RING.Position),
								MoveToPos = nil,
								RotationX = 0,
								RotationY = 0,
								RotationZ = 0,
								Material = "Neon",
								Color = C3(1, 0, 0),
								SoundID = BLASTS[MRANDOM(1, #BLASTS)],
								SoundPitch = MRANDOM(9, 12) / 10,
								SoundVolume = 10
							})
							for e = 1, 3 do
								WACKYEFFECT({
									EffectType = "Wave",
									Size = VT(25, 0, 25),
									Size2 = VT(40, 0, 40) + VT(e * 6, e / 5, e * 6),
									Transparency = 0,
									Transparency2 = 1,
									CFrame = CF(RING.Position) * ANGLES(RAD(0), RAD(72 * i), RAD(0)),
									MoveToPos = nil,
									RotationX = 0,
									RotationY = 3,
									RotationZ = 0,
									Material = "Neon",
									Color = C3(1, 0, 1),
									SoundID = nil,
									SoundPitch = nil,
									SoundVolume = nil
								})
								WACKYEFFECT({
									Time = 35,
									EffectType = "Sphere",
									Size = VT(22, 45, 22),
									Size2 = VT(25, 45 + e * 75, 25),
									Transparency = 0,
									Transparency2 = 1,
									CFrame = CF(RING.Position) * TURN,
									MoveToPos = nil,
									RotationX = 0,
									RotationY = 0,
									RotationZ = 0,
									Material = "Neon",
									Color = C3(0, .5, 0),
									SoundID = nil,
									SoundPitch = MRANDOM(9, 12) / 10,
									SoundVolume = 10
								})
							end
							wait(0.3)
							DELET()
						end))
					end
				end
			end
		end
	end))
	Rooted = false
	DELET()
	GYRO:remove()
	ATTACK = false
	Rooted = false
end

function CreateFlyingDebree(FLOOR, POSITION, AMOUNT, BLOCKSIZE, SWAIT, STRENGTH)
	if FLOOR ~= nil then
		for i = 1, AMOUNT do
			do
				local DEBREE = CreatePart(3, Effects, "Neon", FLOOR.Reflectance, 0, "Peal", "Debree", BLOCKSIZE, false)
				DEBREE.Material = FLOOR.Material
				DEBREE.Color = FLOOR.Color
				DEBREE.CFrame = POSITION * ANGLES(RAD(MRANDOM(-360, 360)), RAD(MRANDOM(-360, 360)), RAD(MRANDOM(-360, 360)))
				DEBREE.Velocity = VT(MRANDOM(-STRENGTH, STRENGTH), STRENGTH, MRANDOM(-STRENGTH, STRENGTH))
				coroutine.resume(coroutine.create(function()
					Swait(15)
					DEBREE.Parent = workspace
					DEBREE.CanCollide = true
					Debris:AddItem(DEBREE, SWAIT)
				end))
			end
		end
	end
end

function CreateFlyingDebree2(FLOOR,POSITION,AMOUNT,BLOCKSIZE,SWAIT,STRENGTH)
	if FLOOR ~= nil then
		for i = 1, AMOUNT do
			local DEBREE = CreatePart(3, Effects, "Neon", 0, 0, "Really black", "Debree", BLOCKSIZE, false)
			DEBREE.Material = FLOOR.Material
			DEBREE.Color = FLOOR.Color
			DEBREE.CFrame = POSITION * ANGLES(RAD(MRANDOM(-360,360)),RAD(MRANDOM(-360,360)),RAD(MRANDOM(-360,360)))
			DEBREE.Velocity = VT(MRANDOM(-STRENGTH,STRENGTH),STRENGTH,MRANDOM(-STRENGTH,STRENGTH))
			coroutine.resume(coroutine.create(function()
				Swait(15)
				DEBREE.Parent = workspace
				DEBREE.CanCollide = true
				Debris:AddItem(DEBREE,SWAIT)
			end))
		end
	end
end

function CreateDebreeRing2(FLOOR,POSITION,SIZE,BLOCKSIZE,SWAIT)
	if FLOOR ~= nil then
		coroutine.resume(coroutine.create(function()
			local PART = CreatePart(3, Effects, "Plastic", 0, 1, "Lime green", "DebreeCenter", VT(0,0,0))
			PART.CFrame = CF(POSITION)
			for i = 1, 45 do
				local RingPiece = CreatePart(3, Effects, "Plastic", 0, 0, "Really blue", "DebreePart", BLOCKSIZE)
				RingPiece.Material = FLOOR.Material
				RingPiece.Color = FLOOR.Color
				RingPiece.CFrame = PART.CFrame * ANGLES(RAD(0), RAD(i*8), RAD(0)) * CF(SIZE*4, 0, 0) * ANGLES(RAD(MRANDOM(-360,360)),RAD(MRANDOM(-360,360)),RAD(MRANDOM(-360,360)))
				Debris:AddItem(RingPiece,SWAIT)
			end
			PART:remove()
		end))
	end
end

function CreateDebreeRing(FLOOR, POSITION, SIZE, BLOCKSIZE, SWAIT)
	if FLOOR ~= nil then
		coroutine.resume(coroutine.create(function()
			local PART = CreatePart(3, Effects, "Plastic", 0, 1, "Pearl", "DebreeCenter", VT(0, 0, 0))
			PART.CFrame = CF(POSITION)
			for i = 1, 45 do
				local RingPiece = CreatePart(3, Effects, "Plastic", 0, 0, "Pearl", "DebreePart", BLOCKSIZE)
				RingPiece.Material = FLOOR.Material
				RingPiece.Color = FLOOR.Color
				RingPiece.CFrame = PART.CFrame * ANGLES(RAD(0), RAD(i * 8), RAD(0)) * CF(SIZE * 4, 0, 0) * ANGLES(RAD(MRANDOM(-360, 360)), RAD(MRANDOM(-360, 360)), RAD(MRANDOM(-360, 360)))
				Debris:AddItem(RingPiece, SWAIT)
			end
			PART:remove()
		end))
	end
end

function BIGSMASH()
	local HITFLOOR, HITPOS = Raycast(Mouse.Hit.p + VT(0, 1, 0), CF(Mouse.Hit.p + VT(0, 10, 0), Mouse.Hit.p - VT(0, 10, 0)).lookVector, 25, Character)
	chatfunc("Die.")
	if HITFLOOR then
		local ORIGINPOS = VT(RootPart.Position.X, HITPOS.Y + 8, RootPart.Position.Z)
		CreateSound("1295446488", Torso, 5, 1)
		for i = 1, 5 do
			WACKYEFFECT({
				Time = MRANDOM(15, 35),
				EffectType = "Round Slash",
				Size = VT(0, 0, 0),
				Size2 = VT(0.3, 0, 0.3),
				Transparency = 0.5,
				Transparency2 = 1,
				CFrame = CF(Torso.Position) * ANGLES(RAD(MRANDOM(-25, 25)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(-25, 25))),
				MoveToPos = nil,
				RotationX = MRANDOM(-50, 50) / 10,
				RotationY = MRANDOM(-50, 50) / 10,
				RotationZ = MRANDOM(-50, 50) / 10,
				Material = "Neon",
				Color = C3(1, 1, 1),
				SoundID = nil,
				SoundPitch = nil,
				SoundVolume = nil
			})
		end
		ATTACK = true
		Rooted = true
		UNANCHOR = false
		RootPart.Anchored = true
		RootPart.CFrame = CF(HITPOS + VT(0, 8, 0), ORIGINPOS) * ANGLES(RAD(0), RAD(180), RAD(0))
		for i = 1, 5 do
			WACKYEFFECT({
				Time = MRANDOM(15, 35),
				EffectType = "Round Slash",
				Size = VT(0, 0, 0),
				Size2 = VT(0.3, 0, 0.3),
				Transparency = 0.5,
				Transparency2 = 1,
				CFrame = CF(Torso.Position) * ANGLES(RAD(MRANDOM(-25, 25)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(-25, 25))),
				MoveToPos = nil,
				RotationX = MRANDOM(-50, 50) / 10,
				RotationY = MRANDOM(-50, 50) / 10,
				RotationZ = MRANDOM(-50, 50) / 10,
				Material = "Neon",
				Color = C3(1, 1, 1),
				SoundID = nil,
				SoundPitch = nil,
				SoundVolume = nil
			})
		end
		for i = 0, 0.2, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(90), RAD(0), RAD(150)), 2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(-12)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 2 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 2 / Animation_Speed)
		end
		for i = 0, 1, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(90), RAD(0), RAD(300)), 0.02 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-45)), 0.02 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(-12)) * RIGHTSHOULDERC0, 0.02 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 0.02 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.02 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 0.02 / Animation_Speed)
		end
		for i = 1, 10 do
			Swait()
			RootPart.CFrame = RootPart.CFrame * CF(0, -0.4, 0)
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(90), RAD(0), RAD(300)), 1.7 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-45)), 1.7 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(-12)) * RIGHTSHOULDERC0, 1.7 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 1.7 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1.7 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1.7 / Animation_Speed)
		end
		for i = 0, 0.2, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(90), RAD(0), RAD(90)), 1.5 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 1.5 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 1.5 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 1.5 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1.5 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1.5 / Animation_Speed)
		end
		local HITFLOOR, HITPOS = Raycast(RightArm.Position, CF(RightArm.Position, RightArm.Position + VT(0, -1, 0)).lookVector, 8, Character)
		if HITFLOOR then
			if HITFLOOR.Parent:FindFirstChildOfClass("Humanoid") then
				local CHILDREN = HITFLOOR.Parent:GetDescendants()
				for index, CHILD in pairs(CHILDREN) do
					if CHILD:IsA("BasePart") and CHILD.Parent:FindFirstChildOfClass("Humanoid") then
						for i = 1, 5 do
							CreateFlyingDebree(CHILD, CF(CHILD.Position), 1, CHILD.Size / 2, 5, MRANDOM(15, 25))
							CHILD:remove()
						end
					end
				end
				local SOUNDPART = CreatePart(3, Effects, "Grass", 0, 1, "Really black", "Sound", VT(0, 0, 0))
				SOUNDPART.CFrame = CF(HITPOS)
				Debris:AddItem(SOUNDPART, 5)
				CreateSound("130972023", SOUNDPART, 6, 3)
				CreateSound("182765513", SOUNDPART, 6, 1)
				WACKYEFFECT({
					EffectType = "Ring",
					Size = VT(0, 0, 0),
					Size2 = VT(1, 1, 0),
					Transparency = 0.7,
					Transparency2 = 1,
					CFrame = CF(HITPOS) * ANGLES(RAD(90), RAD(0), RAD(0)),
					MoveToPos = nil,
					RotationX = 0,
					RotationY = 0,
					RotationZ = 0,
					Material = "Neon",
					Color = C3(1, 1, 1),
					SoundID = nil,
					SoundPitch = nil,
					SoundVolume = nil
				})
			elseif HITFLOOR.Parent.Parent:FindFirstChildOfClass("Humanoid") then
				local CHILDREN = HITFLOOR.Parent.Parent:GetDescendants()
				for index, CHILD in pairs(CHILDREN) do
					if CHILD:IsA("BasePart") and CHILD.Parent:FindFirstChildOfClass("Humanoid") then
						for i = 1, 5 do
							CreateFlyingDebree(CHILD, CF(CHILD.Position), 1, CHILD.Size / 2, 5, MRANDOM(15, 25))
							CHILD:remove()
						end
					end
				end
				local SOUNDPART = CreatePart(3, Effects, "Grass", 0, 1, "Really black", "Sound", VT(0, 0, 0))
				SOUNDPART.CFrame = CF(HITPOS)
				Debris:AddItem(SOUNDPART, 5)
				CreateSound("130972023", SOUNDPART, 6, 3)
				CreateSound("182765513", SOUNDPART, 6, 1)
				WACKYEFFECT({
					EffectType = "Ring",
					Size = VT(0, 0, 0),
					Size2 = VT(1, 1, 0),
					Transparency = 0.7,
					Transparency2 = 1,
					CFrame = CF(HITPOS) * ANGLES(RAD(90), RAD(0), RAD(0)),
					MoveToPos = nil,
					RotationX = 0,
					RotationY = 0,
					RotationZ = 0,
					Material = "Neon",
					Color = C3(1, 1, 1),
					SoundID = nil,
					SoundPitch = nil,
					SoundVolume = nil
				})
			elseif HITFLOOR.Anchored == false then
				if HITFLOOR.Parent ~= workspace then
					local CHILDREN = HITFLOOR.Parent:GetDescendants()
					for index, CHILD in pairs(CHILDREN) do
						if CHILD:IsA("BasePart") and CHILD.Position.Y < HITPOS.Y then
							for i = 1, 5 do
								CreateFlyingDebree(CHILD, CF(CHILD.Position), 1, CHILD.Size / 3, 5, MRANDOM(15, 25))
							end
							CHILD:remove()
						end
					end
				else
					for i = 1, 5 do
						CreateFlyingDebree(HITFLOOR, CF(HITFLOOR.Position), 1, HITFLOOR.Size / 3, 5, MRANDOM(15, 25))
					end
					HITFLOOR:remove()
				end
				local SOUNDPART = CreatePart(3, Effects, "Grass", 0, 1, "Really black", "Sound", VT(0, 0, 0))
				SOUNDPART.CFrame = CF(HITPOS)
				Debris:AddItem(SOUNDPART, 5)
				CreateSound("130972023", SOUNDPART, 10, 3)
				CreateSound("178452217", SOUNDPART, 6, 1)
				WACKYEFFECT({
					EffectType = "Ring",
					Size = VT(0, 0, 0),
					Size2 = VT(1, 1, 0),
					Transparency = 0.7,
					Transparency2 = 1,
					CFrame = CF(HITPOS) * ANGLES(RAD(90), RAD(0), RAD(0)),
					MoveToPos = nil,
					RotationX = 0,
					RotationY = 0,
					RotationZ = 0,
					Material = "Neon",
					Color = C3(1, 1, 1),
					SoundID = nil,
					SoundPitch = nil,
					SoundVolume = nil
				})
			else
				local SOUNDPART = CreatePart(3, Effects, "Grass", 0, 1, "Really black", "Sound", VT(0, 0, 0))
				SOUNDPART.CFrame = CF(HITPOS)
				Debris:AddItem(SOUNDPART, 5)
				CreateSound("130972023", SOUNDPART, 10, 1)
				CreateSound("130972023", SOUNDPART, 6, 0.7)
				CreateDebreeRing(HITFLOOR, HITPOS, 5, VT(5, 5, 5), 5)
				CreateDebreeRing(HITFLOOR, HITPOS, 8, VT(8, 8, 8), 5)
				ApplyAoE5(HITPOS, 15, 45, 75, 75, true)
				ApplyAoE5(HITPOS, 25, 25, 35, 35, true)
				for i = 1, 5 do
					CreateFlyingDebree(HITFLOOR, CF(HITPOS), 1, VT(MRANDOM(10, 30) / 10, MRANDOM(10, 30) / 10, MRANDOM(10, 30) / 10), 5, MRANDOM(75, 150))
				end
				for i = 1, 5 do
					CreateFlyingDebree(HITFLOOR, CF(HITPOS), 1, VT(MRANDOM(10, 30) / 2, MRANDOM(10, 30) / 2, MRANDOM(10, 30) / 2), 5, MRANDOM(75, 150))
				end
			end
		end
		for i = 0, 1.2, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(90), RAD(0), RAD(90)), 1.5 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 1.5 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(40)) * RIGHTSHOULDERC0, 1.5 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 1.5 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1.5 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.5, -0.5) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1.5 / Animation_Speed)
		end
		ATTACK = false
		Rooted = false
		UNANCHOR = true
		RootPart.Anchored = false
	end
end


function MagicSphere3(SIZE,WAIT,CFRAME,COLOR,GROW)
	local wave = CreatePart(3, Effects, "Neon", 0, 0, BRICKC(COLOR), "Effect", VT(1,1,1), true)
	local mesh = IT("SpecialMesh",wave)
	mesh.MeshType = "Sphere"
	mesh.Scale = SIZE
	mesh.Offset = VT(0,0,0)
	wave.CFrame = CFRAME
	coroutine.resume(coroutine.create(function(PART)
		for i = 1, WAIT do
			Swait()
			mesh.Scale = mesh.Scale + GROW
			wave.Transparency = wave.Transparency + (1/WAIT)
			if wave.Transparency > 0.99 then
				wave:remove()
			end
		end
	end))
end

function CreateRing2(SIZE,DOESROT,ROT,WAIT,CFRAME,COLOR,GROW)
	local wave = CreatePart(3, Effects, "Neon", 0, 0.5, BRICKC(COLOR), "Effect", VT(0,0,0))
	local mesh = IT("SpecialMesh",wave)
	mesh.MeshType = "FileMesh"
	mesh.MeshId = "http://www.roblox.com/asset/?id=3270017"
	mesh.Scale = SIZE
	mesh.Offset = VT(0,0,0)
	wave.CFrame = CFRAME
	coroutine.resume(coroutine.create(function(PART)
		for i = 1, WAIT do
			Swait()
			mesh.Scale = mesh.Scale + GROW
			if DOESROT == true then
				wave.CFrame = wave.CFrame * CFrame.fromEulerAnglesXYZ(0,ROT,0)
			end
			wave.Transparency = wave.Transparency + (0.5/WAIT)
			if wave.Transparency > 0.99 then
				wave:remove()
			end
		end
	end))
end


function ApplyAoE4(POSITION, RANGE, MINDMG, MAXDMG, FLING, IZBANISH)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character then
			local HUM = CHILD:FindFirstChildOfClass("Humanoid")
			if HUM then
				local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
				if TORSO and RANGE >= (TORSO.Position - POSITION).Magnitude then
					if IZBANISH == true then
						Banish(CHILD)
					else
						local DMG = MRANDOM(MINDMG, MAXDMG)
						ApplyDamage(HUM, DMG, TORSO)
					end
					if FLING > 0 then
						for _, c in pairs(CHILD:GetChildren()) do
							if c:IsA("BasePart") then
								local bv = Instance.new("BodyVelocity")
								bv.maxForce = Vector3.new(1000000000, 1000000000, 1000000000)
								bv.velocity = CF(POSITION, TORSO.Position).lookVector * FLING
								bv.Parent = c
								Debris:AddItem(bv, 0.05)
							end
						end
					end
				end
			end
		end
	end
end

function ApplyAoE6(POSITION, RANGE, MINDMG, MAXDMG, FLING, KILLD)
	local CHILDREN = workspace:GetDescendants()
	for index, CHILD in pairs(CHILDREN) do
		if CHILD.ClassName == "Model" and CHILD ~= Character then
			local HUM = CHILD:FindFirstChildOfClass("Humanoid")
			if HUM then
				local TORSO = CHILD:FindFirstChild("Torso") or CHILD:FindFirstChild("UpperTorso")
				if TORSO and RANGE >= (TORSO.Position - POSITION).Magnitude then
					if KILLD == true then
						Kill2(CHILD)
					else
						local DMG = MRANDOM(MINDMG, MAXDMG)
						ApplyDamage(HUM, DMG, TORSO)
					end
					if FLING > 0 then
						for _, c in pairs(CHILD:GetChildren()) do
							if c:IsA("BasePart") then
								local bv = Instance.new("BodyVelocity")
								bv.maxForce = Vector3.new(1000000000, 1000000000, 1000000000)
								bv.velocity = CF(POSITION, TORSO.Position).lookVector * FLING
								bv.Parent = c
								Debris:AddItem(bv, 0.05)
							end
						end
					end
				end
			end
		end
	end
end

function Complete_Control()
ATTACK = true
Rooted = true
        chatfunc("Do Not Try To Get Evade This.")
        CreateRing2(VT(0,0,0),false,0,45,RootPart.CFrame*ANGLES(RAD(90),RAD(0),RAD(0)),"Crimson",VT(100,100,100))
	    CreateSound("1137548130", Effects, 10, 1)
	    MagicSphere3(VT(0,0,0),45,Torso.CFrame,"Maroon",VT(500,500,500))
		ApplyAoE4(Torso.Position, 9999, 0, 0, 0, true)
ATTACK = false
Rooted = false
end

function Taunt()
    ATTACK = true
    Rooted = false
        for i=0, 2, 0.1 / Animation_Speed do
        Swait()
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(45), RAD(0), RAD(0)), 1 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
       CreateSound(300208779, Head, 5, 1, false)
        for i=0, 4, 0.1 / Animation_Speed do
        Swait()
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 - 0.1 * COS(SINE / 3) + ((1) - 1)) * ANGLES(RAD(-45 - 2.5 * SIN(SINE / 12)), RAD(7 * COS(SINE / 24)), RAD(0)), 0.5 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, - 0.1 * COS(SINE / 3)) * ANGLES(RAD(-21), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, - 0.1 * COS(SINE / 3)) * ANGLES(RAD(-21), RAD(0), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.2 + 0.05 * COS(SINE / 4)) * ANGLES(RAD(-32), RAD(0), RAD(0)), 1 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 4), -0.01) * ANGLES(RAD(-32), RAD(80), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 4), -0.01) * ANGLES(RAD(-32), RAD(-80), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
    ATTACK = false
    Rooted = false
    end
 
function Taunt3()
    ATTACK = true
    Rooted = true
    local TAUNT = CreateSound(189035671, Torso, 7, 1, false)
    repeat
        Swait()
        TAUNT.Parent = Torso
        TAUNT.Playing = true
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(-0.02, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(25), RAD(0)), 1 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.1, 0.5, -0.45) * ANGLES(RAD(110), RAD(0), RAD(-80)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.1, 0.15, -0.45) * ANGLES(RAD(80), RAD(0), RAD(80)) * ANGLES(RAD(0), RAD(45), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), 0) * ANGLES(RAD(0), RAD(80), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), 0) * ANGLES(RAD(0), RAD(-80), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
    until TAUNT.TimePosition >= 1.25
    for i=0, 1.6, 0.1 / Animation_Speed do
        Swait()
        TAUNT.Parent = Torso
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(5)), 1 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(25)), 1 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.1, 0.5, -0.45) * ANGLES(RAD(110), RAD(0), RAD(-80)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.1, 0.15, -0.45) * ANGLES(RAD(80), RAD(0), RAD(80)) * ANGLES(RAD(0), RAD(45), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), 0) * ANGLES(RAD(0), RAD(75), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), 0) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
    end
    ATTACK = false
    Rooted = false
end

function TomYumTaunt()
	    ATTACK = true
	    Rooted = true
		for i=0, 1, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(0), RAD(0), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		CameraEnshaking(1,45)
		CreateSound(363808674, Torso, 6, 1, false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25*SIZE) - 1)) * ANGLES(RAD(35), RAD(0), RAD(-25)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(40), RAD(-50), RAD(200)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.3*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40), RAD(-50), RAD(-200)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-83), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		ATTACK = false
		Rooted = false
end

function Warp()
	ATTACK = true
	Rooted = true
	coroutine.resume(coroutine.create(function()
    Swait(38)
	WACKYEFFECT({Time = 50, EffectType = "Box", Size = VT(20,20,20)*SIZE, Size2 = VT(0,0,0), Transparency = 0, Transparency2 = 1, CFrame = Torso.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(153, 0/255, 153), SoundID = 289556450, SoundPitch = 0.96, SoundVolume = 10})
    end))
	for i=0, 0.5, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 1) * ANGLES(RAD(-15), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.15*SIZE, 0.5*SIZE, -0.8*SIZE) * ANGLES(RAD(90), RAD(0), RAD(-45)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.15*SIZE, 0.5*SIZE, -0.8*SIZE) * ANGLES(RAD(90), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE, -0.01*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE, -0.01*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
	end
	for i=0, 1, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 10) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.15*SIZE, 0.5*SIZE, -0.8*SIZE) * ANGLES(RAD(90), RAD(0), RAD(-45)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.15*SIZE, 0.5*SIZE, -0.8*SIZE) * ANGLES(RAD(90), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE, -0.01*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE, -0.01*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
	end
	local ORIGIN = RootPart.Position
	RootPart.CFrame = CF(Mouse.Hit.p+VT(0,4,0),ORIGIN)
	for i=0, 0.3, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 1) * ANGLES(RAD(-15), RAD(0), RAD(-45)), 0.5 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.25) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.15, 0.5*SIZE, -0.8*SIZE) * ANGLES(RAD(90), RAD(0), RAD(-45)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.15, 0.5*SIZE, -0.8*SIZE) * ANGLES(RAD(90), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE, -0.01*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE, -0.01*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
	end
	RootPart.Anchored = false
	ATTACK = false
	Rooted = false
end

function Bullet()
	ATTACK = true
	Rooted = false
	Weapon2.Parent = Character
	for i=0, 0.4, 0.1 / Animation_Speed do
		Swait()
		turnto(Mouse.Hit.p)
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(90)), 0.5 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * SIZE, 0 * SIZE, 0 + ((1 * SIZE) - 1)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(90), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.6*SIZE, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE + 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-2 - 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(-75), RAD(0)) * ANGLES(RAD(-2 + 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	repeat
		for i=0, 0.2, 0.1 / Animation_Speed do
			Swait()
			turnto(Mouse.Hit.p)
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 - 0.04 * SIN(SINE / 24)*SIZE, 0 + 0.04 * SIN(SINE / 12)*SIZE, 0 + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0 - 2.5 * SIN(SINE / 24)), RAD(40)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0.4) * ANGLES(RAD(-10), RAD(0), RAD(-32)), 0.5 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(90), RAD(0), RAD(40)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.6*SIZE, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE + 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-2 - 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(-75), RAD(0)) * ANGLES(RAD(-2 + 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
		local HIT,POS = CastProperRay(Hole.Position, Mouse.Hit.p, 1000, Character)
		SpawnTrail(Hole.Position,POS)
		ApplyAoE3(POS,1,3,100,20,false)
		if HIT ~= nil then
			if HIT.Parent ~= workspace and HIT.Parent.ClassName ~= "Folder" then
			end
		end
		---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 0, Transparency2 = 1, CFrame = Hole.CFrame, MoveToPos = Hole.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
		---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = Hole.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 5, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = 213603013, SoundPitch = MRANDOM(8,11)/10, SoundVolume = 8})
		---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = CF(POS,Hole.Position) * ANGLES(RAD(-90), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = -5, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = MRANDOM(8,11)/10, SoundVolume = 8})
		---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = CF(POS,Hole.Position) * ANGLES(RAD(-90), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = 5, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = MRANDOM(8,11)/10, SoundVolume = 8})
		for i=0, 0.3, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 - 0.04 * SIN(SINE / 24)*SIZE, 0 + 0.04 * SIN(SINE / 12)*SIZE, 0 + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0 - 2.5 * SIN(SINE / 24)), RAD(40)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0.4) * ANGLES(RAD(5), RAD(0), RAD(-32)), 0.25 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(90), RAD(15), RAD(40)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.6*SIZE, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE + 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(75), RAD(0)) * ANGLES(RAD(-2 - 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(-75), RAD(0)) * ANGLES(RAD(-2 + 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
	until KEYHOLD == false
	Weapon2.Parent = nil
	ATTACK = false
	Rooted = false
end


function AttackTemplate()
    ATTACK = true
    Rooted = false
    for i=0, 1, 0.1 / Animation_Speed do
        Swait()
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.1 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 0.1 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 0.1 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 0.1 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
    end
    ATTACK = false
    Rooted = false
end
 
function GoldenPunch()
    ATTACK = true
    Rooted = false
    local SPEED = Speed
    Speed = 8
    for i=0, 4, 1 / Animation_Speed do
        Swait()
        turnto(Mouse.Hit.p)
        MagicSphere(VT(1,1,1),15,RightArm.CFrame * CF(MRANDOM(-3,3),MRANDOM(-3,3),MRANDOM(-3,3)),"Really black",VT(-1/15,-1/15,-1/15))
        MagicSphere(VT(2,2,2),15,RightArm.CFrame * CF(MRANDOM(-3,3),MRANDOM(-3,3),MRANDOM(-3,3)),"Really black",VT(-2/15,-2/15,-2/15))
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 * Player_Size, 0 * Player_Size, -0.2 * Player_Size + 0.05 * COS(SINE / 12) * Player_Size) * ANGLES(RAD(0), RAD(0), RAD(-85)), 0.15 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * Player_Size, 0 * Player_Size, 0 + ((1 * Player_Size) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(85)), 0.2 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90+(MRANDOM(-45,45)/10)), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 3 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90), RAD(0), RAD(-85)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
    end
    for i=0, 0.15, 1 / Animation_Speed do
        Swait()
        turnto(Mouse.Hit.p)
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 * Player_Size, 0 * Player_Size, -0.2 * Player_Size + 0.05 * COS(SINE / 12) * Player_Size) * ANGLES(RAD(0), RAD(0), RAD(65)), 1 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * Player_Size, 0 * Player_Size, 0 + ((1 * Player_Size) - 1)) * ANGLES(RAD(25), RAD(0), RAD(35)), 1 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90), RAD(0), RAD(65)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(-20), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 1 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
    end
    local PART = CreatePart(3, Effects, "Neon", 0, 0.8, "Really black", "Punch", VT(50,50,50),false)
    PART.CFrame = RootPart.CFrame * CF(0,0,-25)
    PART.Shape = "Ball"
    local bv = Instance.new("BodyVelocity")
    bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
    bv.velocity = RootPart.CFrame.lookVector*600
    bv.Parent = PART
    bv.Name = "PROJECTILEVELOCITY"
    CreateWave(VT(1,5,1),55,RootPart.CFrame * CF(0,0,-6)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,-1,"Really black",VT(2.5,0.2,2.5))
    CreateWave(VT(1,5,1),55,RootPart.CFrame * CF(0,0,-6)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,1,"Really black",VT(3,0.2,3))
    CreateSwirl(VT(3,5,3),75,RootPart.CFrame * CF(0,0,-15)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,-1,"Really black",VT(2,0.6,2))
    CreateSwirl(VT(3,5,3),75,RootPart.CFrame * CF(0,0,-15)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,1,"Really black",VT(2.2,0.6,2.2))
    CreateSound("824687369", Effects, 10, MRANDOM(10, 12) / 10)
    CreateSound("159882598", Effects, 10, MRANDOM(10, 10) / 10)
    coroutine.resume(coroutine.create(function()
        for i = 1, 10 do
            Swait()
            PART.Transparency = PART.Transparency + 0.2/10
            PART.Size = PART.Size + VT(5,5,5)
            ApplyAoE3(PART.Position,35,10,99999,99999,false)
        end
        PART:Destroy()
    end))
    for i=0, 1, 0.1 / Animation_Speed do
        Swait()
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 * Player_Size, 0 * Player_Size, -0.2 * Player_Size + 0.05 * COS(SINE / 12) * Player_Size) * ANGLES(RAD(15), RAD(0), RAD(95)), 2 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * Player_Size, 0 * Player_Size, 0 + ((1 * Player_Size) - 1)) * ANGLES(RAD(25), RAD(0), RAD(35)), 3 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 3 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(-70), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 3 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1 * Player_Size, -0.8 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-15), RAD(0), RAD(0)), 3 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(0)), 3 / Animation_Speed)
    end
    Speed = SPEED
    ATTACK = false
    Rooted = false
end
 
function GoldenGrasp()
    ATTACK = true
    Rooted = false
    local HASGRABBED = false
    local WELD = nil
    local TORS = nil
    local hitting = RightArm.Touched:connect(function(hit)
        if hit.Parent:FindFirstChild("Humanoid") then
            if hit.Parent.Humanoid.Health ~= 0 then
                UNANCHOR = false
                TORS = hit.Parent:FindFirstChild("Torso") or hit.Parent:FindFirstChild("UpperTorso")
                TORS.Anchored = true
                TORS.CFrame = RightArm.CFrame * CF(0,-1.15,0) * ANGLES(RAD(90), RAD(0), RAD(0))
                WELD = weldBetween(RightArm,TORS)
                HASGRABBED = true
                Rooted = true
            end
        end
    end)
    for i=0, 1, 0.1 / Animation_Speed do
        Swait()
        if HASGRABBED == true then
            break
        end
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(25)), 2 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-25)), 2 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1, 0.5, -1) * ANGLES(RAD(90), RAD(0), RAD(-25)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 2 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
    end
    hitting:disconnect()
    if HASGRABBED == true then
        TORS.Anchored = false
        RootPart.Anchored = true
        for i=0, 2, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, -1.2) * ANGLES(RAD(65), RAD(0), RAD(0)), 0.2 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(-40), RAD(0), RAD(20)) * RIGHTSHOULDERC0, 0.2 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(-40), RAD(0), RAD(-20)) * LEFTSHOULDERC0, 0.2 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.3, -1) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-20)), 0.2 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.3, -1) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
        end
        CreateWave(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),false,2,"Pearl",VT(0.5,0.3,0.5))
        CreateWave(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),true,2,"Pearl",VT(0.6,0.3,0.6))
        local SOUNDPART = CreatePart(3, Effects, "Neon", 0, 1, "Pearl", "Sound", VT(0,0,0))
        SOUNDPART.CFrame = RootPart.CFrame
        Debris:AddItem(SOUNDPART,5)
        CreateSound("1295446488", SOUNDPART, 2, 1)
        for i = 1, 15 do
            Slice(0.1,65,CF(RootPart.Position) * ANGLES(RAD(0), RAD(MRANDOM(-180,180)), RAD(90)),"Pearl", 1 + MRANDOM(1,30)/15)
        end
        for i=0, 2, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 50000000) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(-20), RAD(0), RAD(0)), 0.2 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(-40), RAD(0), RAD(20)) * RIGHTSHOULDERC0, 0.2 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(-40), RAD(0), RAD(-20)) * LEFTSHOULDERC0, 0.2 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, -0.3) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(-20)), 0.2 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, -0.3) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(20)), 0.2 / Animation_Speed)
        end
        for i=0, 0.25, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(90), RAD(0), RAD(0)), 3 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 3 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 3 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 3 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
        end
        if HITFLOOR ~= nil then
            CreateDebreeRing(HITFLOOR,CF(RootPart.Position)*CF(0,-3,0).p,15,VT(7,7,7),85)
        end
        CreateSound("414517163", Effects, 10, MRANDOM(7, 12) / 10)
        WELD:remove()
        for i = 1, 25 do
            Swait()
            killnearest(RootPart.Position,45,100,Torso.CFrame)
            CreateWave(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),true,5,"Pearl",VT(0.3,3,0.3))
            CreateWave(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),false,2,"Really black",VT(1,0.4,1))
            CreateWave(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),true,5,"Really black",VT(2.3,0.3,2.3))
            Slice(0.1,65,CF(RootPart.Position) * ANGLES(RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180))),"Pearl", 1 + MRANDOM(1,30)/5)
            Slice(0.1,65,CF(RightArm.CFrame*CF(0,-1,0).p) * ANGLES(RAD(MRANDOM(-20,20)), RAD(MRANDOM(-180,180)), RAD(MRANDOM(-20,20))),"Really black", 1 + MRANDOM(1,30)/15)
        end
    end
    UNANCHOR = true
    ATTACK = false
    Rooted = false
end
 
function Mach20()
    local ORIGIN = RootPart.Position
    CreateWave2(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),false,2,"Pearl",VT(0.2,3,0.4))
    CreateWave2(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),false,2,"Pearl",VT(0.2,3.1,0.6))
    local SOUNDPART = CreatePart(3, Effects, "Neon", 0, 1, "Pearl", "Sound", VT(0,0,0))
    SOUNDPART.CFrame = RootPart.CFrame
    Debris:AddItem(SOUNDPART,5)
    CreateSound("1295446488", SOUNDPART, 2, 1)
    RootPart.CFrame = CF(Mouse.Hit.p+VT(0,3,0),VT(ORIGIN.X,Mouse.Hit.p.Y,ORIGIN.Z))
    Swait()
    CreateWave2(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),false,2,"Pearl",VT(0.2,3,0.4))
    CreateWave2(VT(3,1,3),65,CF(RootPart.Position)*CF(0,-3,0),false,2,"Pearl",VT(0.2,3.1,0.6))
    local SOUNDPART = CreatePart(3, Effects, "Neon", 0, 1, "Pearl", "Sound", VT(0,0,0))
    SOUNDPART.CFrame = RootPart.CFrame
    Debris:AddItem(SOUNDPART,5)
    CreateSound("1295446488", SOUNDPART, 2, 1)
    if MRANDOM(1,8) == 1 then
        ATTACK = true
        Rooted = true
        local FRUITSTABLE = {
            {MeshId = "16190555", TextureId = "16190577", Color = "Really red"},
            {MeshId = "119574562", TextureId = "64374853", Color = "New Yeller"},
            {MeshId = "24394186", TextureId = "24394178", Color = "Brown"}
        }
        local FRUIT = FRUITSTABLE[MRANDOM(1, #FRUITSTABLE)]
        local FRUITMODEL = CreatePart(3, Effects, "Neon", 0, 0, FRUIT.Color, "Fruit", VT(0,0,0),false)
        CreateMesh("SpecialMesh", FRUITMODEL, "FileMesh", FRUIT.MeshId, FRUIT.TextureId, VT(2,2,2), VT(0,0,0))
        FRUITMODEL.CFrame = RightArm.CFrame * CF(0,-1.5,0)
        weldBetween(RightArm,FRUITMODEL)
        for i=0, 4, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(190), RAD(-145)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
        end
        CreateSound("414517163", RightArm, 10, MRANDOM(7, 12) / 10)
        FRUITMODEL:remove()
        for i = 1, 15 do
            Slice(0.1,15,RightArm.CFrame*CF(0,-1,0) * ANGLES(RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180))),FRUIT.Color, 1 + MRANDOM(1,10)/15)
        end
        for i=0, 0.5, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(190), RAD(-175)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
        end
        ATTACK = false
        Rooted = false
    end
end
 
function ImplosionCollision()
    ATTACK = true
    Rooted = true
    CreateSound("1548599511", Character, 10, 1)
    for i=0, 8, 0.1 / Animation_Speed do
        Swait()
        turnto(Mouse.Hit.p)
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 * Player_Size, 0 * Player_Size, -0.2 * Player_Size + 0.05 * COS(SINE / 12) * Player_Size) * ANGLES(RAD(0), RAD(0), RAD(-85)), 0.15 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * Player_Size, 0 * Player_Size, 0 + ((1 * Player_Size) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(85)), 0.2 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90+(MRANDOM(-45,45)/10)), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 3 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90), RAD(0), RAD(-85)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
    end

    CreateSound("1548599962", Character, 10, 1)
    for i=0, 0.15, 0.1 / Animation_Speed do
        Swait()
        turnto(Mouse.Hit.p)
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 * Player_Size, 0 * Player_Size, -0.2 * Player_Size + 0.05 * COS(SINE / 12) * Player_Size) * ANGLES(RAD(0), RAD(0), RAD(65)), 1 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * Player_Size, 0 * Player_Size, 0 + ((1 * Player_Size) - 1)) * ANGLES(RAD(25), RAD(0), RAD(35)), 1 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90), RAD(0), RAD(65)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(-20), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 1 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
    end
    MagicSphere(VT(4,4,4),100,RootPart.CFrame * CF(0,0,-6),"Really black",VT(-0.04,-0.04,-0.04))
    MagicSphere(VT(3.5,3.5,3.5),100,RootPart.CFrame * CF(0,0,-6),"Really black",VT(-0.035,-0.035,-0.035))
    coroutine.resume(coroutine.create(function()
        Swait(100)
        for i = 1, 5 do
            MagicSphere(VT(150,150,150),75,RootPart.CFrame * CF(0,0,-72*i),"Really black",VT(-150/(150-(15*i)),-150/(150-(15*i)),-150/(150-(15*i))))
            CreateWave(VT(1,5,1),55,RootPart.CFrame * CF(0,0,-6*i)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,-1,"Really black",VT(4.5*i,0.2,4.5*i))
            CreateWave(VT(1,5,1),55,RootPart.CFrame * CF(0,0,-6*i)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,1,"Really black",VT(5*i,0.2,5*i))
            CreateSwirl(VT(3,5,3),75,RootPart.CFrame * CF(0,0,-15*i)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,-1,"Really black",VT(4*i,0.6,4*i))
            CreateSwirl(VT(3,5,3),75,RootPart.CFrame * CF(0,0,-15*i)*ANGLES(RAD(-90),RAD(0),RAD(0)),true,1,"Really black",VT(5.2*i,0.6,5.2*i))
        end
        ApplyAoE3(RootPart.Position,99999,99999,99999,99999,false)
        for i = 1, 5 do
            CreateSound("824687369", Effects, 10, MRANDOM(5, 8) / 2)
            CreateSound("824687369", Effects, 10, MRANDOM(5, 8) / 3)
        end
        coroutine.resume(coroutine.create(function()
            for i = 1, 50 do
                Swait()
                for i = 1, 1 do
                    Slice(0.1,65,CF(RootPart.Position) * ANGLES(RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180))),"Pearl", 1 + MRANDOM(1,30)/5)
                end
                for i = 1, 2 do
                    Slice(0.1,65,CF(RootPart.Position) * ANGLES(RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180)), RAD(MRANDOM(-180,180))),"Really black", 1 + MRANDOM(1,30)/3)
                end
            end
        end))
    end))
    for i=0, 4, 0.1 / Animation_Speed do
        Swait()
        RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 * Player_Size, 0 * Player_Size, -0.2 * Player_Size + 0.05 * COS(SINE / 12) * Player_Size) * ANGLES(RAD(15), RAD(0), RAD(95)), 2 / Animation_Speed)
        Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0 * Player_Size, 0 * Player_Size, 0 + ((1 * Player_Size) - 1)) * ANGLES(RAD(25), RAD(0), RAD(35)), 3 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(90), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 3 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * Player_Size, 0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(-70), RAD(0), RAD(-25)) * LEFTSHOULDERC0, 3 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1 * Player_Size, -0.8 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-15), RAD(0), RAD(0)), 3 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * Player_Size, -1 * Player_Size, -0 * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(0)), 3 / Animation_Speed)
    end
    ATTACK = false
    Rooted = false
end

--SS--

function VoidedTaunt()
ATTACK = true
Rooted = true
local LAUGHS = {834001699,834001752,834001797,834001828}
CreateSound(LAUGHS[MRANDOM(1,#LAUGHS)], Torso, 30, 1, false)
	coroutine.resume(coroutine.create(function()
        for i=0, 3, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 1.5) * ANGLES(RAD(0), RAD(180), RAD(0)), 0.2 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1*SIZE) - 1)) * ANGLES(RAD(0 + 11 * COS(SINE / 1)), RAD(0), RAD(0 + 11 * COS(SINE / 1))), 0.2 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40 + 11 * COS(SINE / 1)), RAD(0), RAD(20 + 11 * COS(SINE / 1))) * RIGHTSHOULDERC0, 0.2 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.5*SIZE, 0) * ANGLES(RAD(-40 + 11 * COS(SINE / 1)), RAD(0), RAD(-20 + 11 * COS(SINE / 1))) * LEFTSHOULDERC0, 0.2 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE, 0*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-20 + 11 * COS(SINE / 1))), 0.2 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE, 0*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
        end
ATTACK = false
Rooted = false
end))
end

function MagicMissiles()
	ATTACK = true
	Rooted = true
	local SELECTING = true
	local SPOTS = {}
	coroutine.resume(coroutine.create(function()
		local LOOP = 0
		repeat
			LOOP = LOOP + 1
			Swait()
			if LOOP >= 10 then
				LOOP = 0
				for i = 1, #SPOTS do
					if SPOTS[i] ~= nil then
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(8,2,8), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(6,3,6), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
					end
				end
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 - 0.04 * SIN(SINE / 24)*SIZE, 0 + 0.04 * SIN(SINE / 12)*SIZE, 0 + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0 - 2.5 * SIN(SINE / 24)), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(-25 - 4 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35*SIZE, 0.65*SIZE, 0*SIZE) * ANGLES(RAD(115), RAD(45), RAD(35)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-35), RAD(25 - 2.5 * SIN(SINE / 12)), RAD(55 - 2.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE + 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(85), RAD(0)) * ANGLES(RAD(-2 - 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(-85), RAD(0)) * ANGLES(RAD(-2 + 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
		until SELECTING == false
		Rooted = false
		repeat
			LOOP = LOOP + 1
			Swait()
			if LOOP >= 10 then
				LOOP = 0
				for i = 1, #SPOTS do
					if SPOTS[i] ~= nil then
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(8,2,8), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(6,3,6), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
					end
				end
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 - 0.04 * SIN(SINE / 24)*SIZE, 0 + 0.04 * SIN(SINE / 12)*SIZE, 0 + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0 - 2.5 * SIN(SINE / 24)), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(0 - 4 * SIN(SINE / 12)), RAD(25), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35*SIZE, 0.65*SIZE, 0*SIZE) * ANGLES(RAD(90), RAD(0), RAD(35)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-35), RAD(25 - 2.5 * SIN(SINE / 12)), RAD(55 - 2.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE + 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(85), RAD(0)) * ANGLES(RAD(-2 - 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(-85), RAD(0)) * ANGLES(RAD(-2 + 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
		until ATTACK == false
	end))
	repeat
		repeat Swait() until HOLD == true
			local DIST = (RootPart.Position - Mouse.Hit.p).Magnitude
			if DIST > 99999 then
				DIST = 99999
			end
			local RAY,RAYPOS = Raycast(RootPart.Position, (CF(RootPart.Position, Mouse.Hit.p)).lookVector, DIST, workspace)	
			local HITFLOOR,HITPOS,NORMAL = Raycast(RAYPOS+VT(0,1,0), (CF(RAYPOS, RAYPOS + VT(0, -1, 0))).lookVector, 25*SIZE, Character)	
			if HITFLOOR ~= nil then
				table.insert(SPOTS,CF(HITPOS,HITPOS+NORMAL) * ANGLES(RAD(90), RAD(0), RAD(0)))
				WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(6,2,6), Transparency = 0, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1.2,0) * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = 90655239, SoundPitch = 1, SoundVolume = 6})
				WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(4,3,4), Transparency = 0, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1.2,0) * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
			end
		repeat Swait() until HOLD == false
	until #SPOTS == 1
	SELECTING = false
	for i = 1, #SPOTS do
		if SPOTS[i] ~= nil then
			local POS = SPOTS[i]
			coroutine.resume(coroutine.create(function()
				local MISSILE = IT("Model",Effects)
				MISSILE.Name = "Missile"
				local BASEPART = CreatePart(3, MISSILE, "Neon", 0, 1, "Miduim stone gray", "Part", VT(2,2,2))
				MakeForm(BASEPART,"Cyl")
				MISSILE.PrimaryPart = BASEPART
				BASEPART.CFrame = POS*CF(0,-30*3,0)
				local HEAD = CreatePart(3, MISSILE, "Neon", 0, 1, "Miduim stone gray", "Part", VT(2,4,2))
				MakeForm(HEAD,"Ball")
				HEAD.CFrame = BASEPART.CFrame*CF(0,1,0)
				local TAIL = CreatePart(3, MISSILE, "Neon", 0, 1, "Miduim stone gray", "Part", VT(2.5,0.1,2.5))
				MakeForm(TAIL,"Cyl")
				TAIL.CFrame = BASEPART.CFrame*CF(0,-1,0)
				for i = 1, 10 do
					Swait()
					for _, c in pairs(MISSILE:GetChildren()) do
						if c.ClassName == "Part" then
							c.Transparency = c.Transparency - 1/10
						end
					end
				end
				for i = 1, 15*1.5 do
					Swait()
					MISSILE:SetPrimaryPartCFrame(BASEPART.CFrame*CF(0,1.35*3,0))
				end
				ApplyAoE3(BASEPART.CFrame.p,20,35,45,75,false)
				WACKYEFFECT({Time = 25, EffectType = "Sphere", Size = VT(0,0,0), Size2 = VT(75,1,75), Transparency = 0.5, Transparency2 = 1, CFrame = BASEPART.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 1.2, SoundVolume = 4})
				WACKYEFFECT({Time = 45, EffectType = "Sphere", Size = VT(0,0,0), Size2 = VT(35,35,35), Transparency = 0, Transparency2 = 1, CFrame = BASEPART.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = 165970126, SoundPitch = MRANDOM(13,15)/10, SoundVolume = 100})
				WACKYEFFECT({Time = 45, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(22,2,22), Transparency = 0, Transparency2 = 1, CFrame = POS * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
				WACKYEFFECT({Time = 45, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(20,3,20), Transparency = 0, Transparency2 = 1, CFrame = POS * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
				MISSILE:remove()
			end))
		end
	end
	wait(0.6)
	ATTACK = false
	Rooted = false
end



function ManiacMissiles()
	ATTACK = true
	Rooted = true
	local SELECTING = true
	local SPOTS = {}
	coroutine.resume(coroutine.create(function()
		local LOOP = 0
		repeat
			LOOP = LOOP + 1
			Swait()
			if LOOP >= 10 then
				LOOP = 0
				for i = 1, #SPOTS do
					if SPOTS[i] ~= nil then
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(8,2,8), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(6,3,6), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
					end
				end
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 - 0.04 * SIN(SINE / 24)*SIZE, 0 + 0.04 * SIN(SINE / 12)*SIZE, 0 + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0 - 2.5 * SIN(SINE / 24)), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(-25 - 4 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35*SIZE, 0.65*SIZE, 0*SIZE) * ANGLES(RAD(115), RAD(45), RAD(35)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-35), RAD(25 - 2.5 * SIN(SINE / 12)), RAD(55 - 2.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE + 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(85), RAD(0)) * ANGLES(RAD(-2 - 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(-85), RAD(0)) * ANGLES(RAD(-2 + 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
		until SELECTING == false
		Rooted = false
		repeat
			LOOP = LOOP + 1
			Swait()
			if LOOP >= 10 then
				LOOP = 0
				for i = 1, #SPOTS do
					if SPOTS[i] ~= nil then
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(8,2,8), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
						WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(6,3,6), Transparency = 0, Transparency2 = 1, CFrame = SPOTS[i], MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
					end
				end
			end
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0 - 0.04 * SIN(SINE / 24)*SIZE, 0 + 0.04 * SIN(SINE / 12)*SIZE, 0 + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0 - 2.5 * SIN(SINE / 24)), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(0 - 4 * SIN(SINE / 12)), RAD(25), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35*SIZE, 0.65*SIZE, 0*SIZE) * ANGLES(RAD(90), RAD(0), RAD(35)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-35), RAD(25 - 2.5 * SIN(SINE / 12)), RAD(55 - 2.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE + 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(85), RAD(0)) * ANGLES(RAD(-2 - 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.06 * SIN(SINE / 24) - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(-85), RAD(0)) * ANGLES(RAD(-2 + 2.5 * SIN(SINE / 24)), RAD(0), RAD(0)), 1 / Animation_Speed)
		until ATTACK == false
	end))
	repeat
		repeat Swait() until HOLD == true
			local DIST = (RootPart.Position - Mouse.Hit.p).Magnitude
			if DIST > 99999 then
				DIST = 99999
			end
			local RAY,RAYPOS = Raycast(RootPart.Position, (CF(RootPart.Position, Mouse.Hit.p)).lookVector, DIST, workspace)	
			local HITFLOOR,HITPOS,NORMAL = Raycast(RAYPOS+VT(0,1,0), (CF(RAYPOS, RAYPOS + VT(0, -1, 0))).lookVector, 25*SIZE, Character)	
			if HITFLOOR ~= nil then
				table.insert(SPOTS,CF(HITPOS,HITPOS+NORMAL) * ANGLES(RAD(90), RAD(0), RAD(0)))
				WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(6,2,6), Transparency = 0, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1.2,0) * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = 90655239, SoundPitch = 1, SoundVolume = 6})
				WACKYEFFECT({Time = 15, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(4,3,4), Transparency = 0, Transparency2 = 1, CFrame = RightArm.CFrame*CF(0,-1.2,0) * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
			end
		repeat Swait() until HOLD == false
	until #SPOTS == 1
	SELECTING = false
	for i = 1, #SPOTS do
		if SPOTS[i] ~= nil then
			local POS = SPOTS[i]
			coroutine.resume(coroutine.create(function()
				local MISSILE = IT("Model",Effects)
				MISSILE.Name = "Missile"
				local BASEPART = CreatePart(3, MISSILE, "Neon", 0, 1, "Dirt brown", "Part", VT(2,2,2))
				MakeForm(BASEPART,"Cyl")
				MISSILE.PrimaryPart = BASEPART
				BASEPART.CFrame = POS*CF(0,-30*3,0)
				local HEAD = CreatePart(3, MISSILE, "Neon", 0, 1, "Dirt brown", "Part", VT(2,4,2))
				MakeForm(HEAD,"Ball")
				HEAD.CFrame = BASEPART.CFrame*CF(0,1,0)
				local TAIL = CreatePart(3, MISSILE, "Neon", 0, 1, "Dirt brown", "Part", VT(2.5,0.1,2.5))
				MakeForm(TAIL,"Cyl")
				TAIL.CFrame = BASEPART.CFrame*CF(0,-1,0)
				for i = 1, 10 do
					Swait()
					for _, c in pairs(MISSILE:GetChildren()) do
						if c.ClassName == "Part" then
							c.Transparency = c.Transparency - 1/10
						end
					end
				end
				for i = 1, 15*1.5 do
					Swait()
					MISSILE:SetPrimaryPartCFrame(BASEPART.CFrame*CF(0,1.35*3,0))
				end
				ApplyAoE3(BASEPART.CFrame.p,20,35,45,75,false)
				WACKYEFFECT({Time = 25, EffectType = "Sphere", Size = VT(0,0,0), Size2 = VT(75,1,75), Transparency = 0.5, Transparency2 = 1, CFrame = BASEPART.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 1.2, SoundVolume = 4})
				WACKYEFFECT({Time = 45, EffectType = "Sphere", Size = VT(0,0,0), Size2 = VT(35,35,35), Transparency = 0, Transparency2 = 1, CFrame = BASEPART.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = 165970126, SoundPitch = MRANDOM(13,15)/10, SoundVolume = 100})
				WACKYEFFECT({Time = 45, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(22,2,22), Transparency = 0, Transparency2 = 1, CFrame = POS * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
				WACKYEFFECT({Time = 45, EffectType = "Wave", Size = VT(0,0,0)*SIZE, Size2 = VT(20,3,20), Transparency = 0, Transparency2 = 1, CFrame = POS * ANGLES(RAD(180), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = 15, RotationZ = 0, Material = "Neon", Color = C3(81, 69, 69), SoundID = nil, SoundPitch = 0.5, SoundVolume = 6})
				MISSILE:remove()
			end))
		end
	end
	wait(0.6)
	ATTACK = false
	Rooted = false
end


local Cooldown1 = false

function Teleport()
	ATTACK = true
	Rooted = false
	for i=0, 0.5, 0.1 / Animation_Speed do
		Swait()
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(15 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.6, 0.75, -0.5) * ANGLES(RAD(0), RAD(-25), RAD(12)) * ANGLES(RAD(125 - 2.5 * COS(SINE / 12) + 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1.5 / Animation_Speed)
	end
	for e = 1, #BODY do
		if BODY[e] ~= nil then
			local STUFF = BODY[e]
			local PART = STUFF[1]
			if PART:IsA("BasePart") and PART ~= RootPart and PART.Name ~= "FaceGradient" and PART.Name ~= "Hair" and PART.Transparency ~= 1 then
				local PRT = PART:Clone()
				PRT.Anchored = true
				PRT.CanCollide = false
				PRT.Material = "Neon"
				PRT.Color = C3(1,1,1)
				PRT.Name = "WarpEffect"
				PRT.Parent = Effects
				PRT.CFrame = PART.CFrame
				PRT:BreakJoints()
				if PRT:FindFirstChildOfClass("Sound") then
					PRT:FindFirstChildOfClass("Sound"):remove()
				end
				if PRT:FindFirstChildOfClass("Decal") then
					PRT:FindFirstChildOfClass("Decal"):remove()
				end
				coroutine.resume(coroutine.create(function()
					for i = 1, 100 do
						Swait()
						PRT.Transparency = PRT.Transparency + 1/100
					end
					PRT:remove()
				end))
			end
		end
	end
	CreateSound(217767125, Torso, 10, 1)
	local POS = RootPart.Orientation
	RootPart.CFrame = CF(Mouse.Hit.p+VT(0,6,0))
	RootPart.Orientation = POS
	RootJoint.Parent = RootPart
	for i=0, 0.5, 0.1 / Animation_Speed do
		Swait()
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(15 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.6, 0.75, -0.5) * ANGLES(RAD(0), RAD(-15), RAD(12)) * ANGLES(RAD(175 - 2.5 * COS(SINE / 12) + 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 2.5 / Animation_Speed)
	end
	for i=0, 0.1, 0.1 / Animation_Speed do
		Swait()
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1, 0.5, 0.5) * ANGLES(RAD(0), RAD(-45), RAD(12)) * ANGLES(RAD(45 - 2.5 * COS(SINE / 12) + 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 0.75 / Animation_Speed)
	end
	ATTACK = false
	Rooted = false
end

function Prison_Key()
	if Mouse.Target ~= nil then
		if Mouse.Target.Parent ~= Character and Mouse.Target.Parent.Parent ~= Character and Mouse.Target.Parent:FindFirstChildOfClass("Humanoid") ~= nil then
			local HUM = Mouse.Target.Parent:FindFirstChildOfClass("Humanoid")
			local TORSO = HUM.Parent:FindFirstChild("Torso") or HUM.Parent:FindFirstChild("UpperTorso")
			local GYRO = IT("BodyGyro",RootPart)
			GYRO.D = 750
			GYRO.P = 20000
			GYRO.MaxTorque = VT(0,40000000,0)
			local grav = Instance.new("BodyPosition",TORSO)
			grav.D = 15
			grav.P = 20000
			grav.maxForce = Vector3.new(math.huge,math.huge,math.huge)
			grav.position = TORSO.Position
			local GYRO2 = IT("BodyGyro",TORSO)
			GYRO2.D = 750
			GYRO2.P = 20000
			GYRO2.MaxTorque = VT(0,40000000,0)
			GYRO2.cframe = CF(TORSO.Position,RootPart.Position)
			grav.Name = "Jail"
			local LOCKPARTS = {}
			ATTACK = true
			Rooted = false
			local LOCK = IT("Model",Effects)
			LOCK.Name = "Lock"
			local LOCK2 = IT("Model",LOCK)
			LOCK2.Name = "Metal"
			--CREATE LOCK--
				local BASE = CreatePart(3, LOCK, "Glass", 0, 0, "Alder", "Keylock", VT(3, 2.5, 1))
				LOCK.PrimaryPart = BASE
				BASE.CFrame = CF(TORSO.Position,RootPart.Position)*CF(0,0,-4)
				local PRT = CreatePart(3, LOCK, "Glass", 0, 0, "Alder", "Keylock", VT(3, 1, 3))
				PRT.CFrame = BASE.CFrame*CF(0,-1.25,0)*ANGLES(RAD(90),RAD(0),RAD(0))
				MakeForm(PRT,"Cyl")
				local PRT = CreatePart(3, LOCK, "Glass", 0, 0, "Alder", "Hole", VT(1, 1, 1))
				PRT.Color = C3(0,0,0)
				PRT.CFrame = BASE.CFrame*CF(0,0.3,-0.01)*ANGLES(RAD(90),RAD(0),RAD(0))
				MakeForm(PRT,"Cyl")
				local PRT = CreatePart(3, LOCK, "Glass", 0, 0, "Alder", "Hole", VT(0.5, 1, 1))
				PRT.Color = C3(0,0,0)
				PRT.CFrame = BASE.CFrame*CF(0,-0.2,-0.01)
				for i = 1, 45 do
					local PRT = CreatePart(3, LOCK2, "Glass", 0, 0, "Alder", "Keylock", VT(0.5, 0.5, 0.5))
					PRT.CFrame = BASE.CFrame*CF(0,2,0)*ANGLES(RAD(0),RAD(0),RAD(-90+(360/90*i)))*CF(0,1,0)
				end
				local PRT = CreatePart(3, LOCK2, "Glass", 0, 0, "Alder", "Keylock", VT(0.5, 0.5, 0.5))
				PRT.CFrame = BASE.CFrame*CF(0,1.5,0)*ANGLES(RAD(0),RAD(0),RAD(90))*CF(0,1,0)
				LOCK2.PrimaryPart = PRT
			---------------
			local CHILDREN = LOCK:GetDescendants()
			for index, CHILD in pairs(CHILDREN) do
				if CHILD:IsA("BasePart") then
					CHILD.Transparency = 1
				end
			end
			for i = 1, 75 do
				LOCK:SetPrimaryPartCFrame(CF(TORSO.Position,RootPart.Position)*CF(0,0,-4))
				Swait()
				GYRO2.cframe = CF(TORSO.Position,RootPart.Position)
				GYRO.cframe = CF(RootPart.Position,TORSO.Position)
				local CHILDREN = LOCK:GetDescendants()
				for index, CHILD in pairs(CHILDREN) do
					if CHILD:IsA("BasePart") then
						CHILD.Transparency = CHILD.Transparency - 1/75
					end
				end
			end
			HUM.DisplayDistanceType = "None"
			local KEY = IT("Model",Effects)
			KEY.Name = "Key"
			--CREATE KEY--
				local KBASE = CreatePart(3, KEY, "Neon", 0, 0, "Alder", "KeyBase", VT(0.1, 1, 0.1),false)
				KEY.PrimaryPart = KBASE
				KBASE.CFrame = RightArm.CFrame*CF(0,-2.1,0)*ANGLES(RAD(0),RAD(90),RAD(0))
				local WLD = weldBetween(RightArm,KBASE)
				for i = 1, 45 do
					local PRT = CreatePart(3, KEY, "Neon", 0, 0, "Alder", "Key", VT(0.1, 0.1, 0.1),false)
					PRT.CFrame = KBASE.CFrame*CF(0,0.8,0)*ANGLES(RAD(0),RAD(0),RAD((360/45*i)))*CF(0,0.25,0)
					weldBetween(KBASE,PRT)
				end
				local PRT = CreatePart(3, KEY, "Neon", 0, 0, "Alder", "Key", VT(0.3, 0.1, 0.1),false)
				PRT.CFrame = KBASE.CFrame*CF(-0.15,-0.45,0)
				weldBetween(KBASE,PRT)
				local PRT = CreatePart(3, KEY, "Neon", 0, 0, "Alder", "Key", VT(0.3, 0.1, 0.1),false)
				PRT.CFrame = KBASE.CFrame*CF(-0.15,-0.25,0)
				weldBetween(KBASE,PRT)
			--------------
			Rooted = true
			Pose("Prepare key",1.5,1.2,false,GYRO,TORSO)
			coroutine.resume(coroutine.create(function()
				for i = 1, 10 do
					Swait()
					GYRO2.cframe = CF(TORSO.Position,RootPart.Position)
					GYRO.cframe = CF(RootPart.Position,TORSO.Position)
				end
				CreateSound(1149318312,BASE,5,1,false)
				CreateSound(160772554,BASE,3,1,false)
				LOCK2:SetPrimaryPartCFrame(BASE.CFrame*CF(0,0.8,0)*ANGLES(RAD(0),RAD(0),RAD(90))*CF(0,1,0))
				for i = 1, 4 do
					WACKYEFFECT({Time = 35, EffectType = "Crystal", Size = VT(1,1,1), Size2 = VT(0,15,0), Transparency = 0, Transparency2 = 1, CFrame = BASE.CFrame*CF(1,1.45,0)*ANGLES(RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360)),RAD(MRANDOM(0,360))), MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = BRICKC"Alder".Color, SoundID = nil, SoundPitch = nil, SoundVolume = nil})
				end
				WACKYEFFECT({Time = 35, EffectType = "Sphere", Size = VT(0,0,0), Size2 = VT(1,1,1)*25, Transparency = 0, Transparency2 = 1, CFrame = TORSO.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 0, RotationZ = 0, Material = "Neon", Color = BRICKC"Alder".Color, SoundID = nil, SoundPitch = MRANDOM(8,12)/10, SoundVolume = 5})
				wait(1)
				TORSO.Parent.Parent = LOCK
				for i = 1, 75 do
					Swait()
					local CHILDREN = KEY:GetDescendants()
					for index, CHILD in pairs(CHILDREN) do
						if CHILD:IsA("BasePart") then
							CHILD.Transparency = i/25
						end
					end
					local CHILDREN = LOCK:GetDescendants()
					for index, CHILD in pairs(CHILDREN) do
						if CHILD:IsA("BasePart") and CHILD.Name ~= "HumanoidRootPart" then
							CHILD.Transparency = CHILD.Transparency + 1/75
						elseif CHILD.ClassName == "Decal" then
							CHILD.Transparency = CHILD.Transparency + 1/75
						end
					end
				end
				TORSO.Parent:ClearAllChildren()
				KEY:remove()
				LOCK:remove()
			end))
			Pose("Turn key",0.8,1.2,false,GYRO,TORSO)
			GYRO:remove()
			ATTACK = false
			Rooted = false
		end
	end
end

function BulletRoller()
	local HITFLOOR,HITPOS = Raycast(RootPart.Position, (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 4, Character)
	if HITFLOOR then
		ATTACK = true
		Rooted = true
		local bv = Instance.new("BodyVelocity") 
		bv.maxForce = Vector3.new(1e9, 1e9, 1e9)
		bv.velocity = CF(RootPart.Position,RootPart.CFrame*CF(0,1,-4).p).lookVector*175
		bv.Parent = RootPart
		local GYRO = IT("BodyGyro",RootPart)
		GYRO.D = 2
		GYRO.P = 20000
		GYRO.MaxTorque = VT(0,4000000,0)
		GYRO.CFrame = CF(RootPart.Position,RootPart.CFrame*CF(0,0,-5).p)
		CreateSound(145486992,Torso,10,0.85,false)
		for i=0, 0.05, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(65), RAD(0), RAD(-10)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(-35), RAD(0), RAD(10)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(50), RAD(-20), RAD(25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(15), RAD(20), RAD(-25)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			if ANIM == "Fall" or ANIM == "Jump" then
				RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.4, -0.6) * ANGLES(RAD(1), RAD(90), RAD(0)) * ANGLES(RAD(-1 * SIN(SINE / 6)), RAD(0), RAD(0)), 1 / Animation_Speed)
				LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-1 * SIN(SINE / 6)), RAD(0), RAD(0)), 1 / Animation_Speed)
			else
				RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(-5), RAD(80), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
				LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-70), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(0)), 1 / Animation_Speed)
			end
		end
		bv:Remove()
		local E = 360/15
		for i = 1, 15 do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(i*E), RAD(0), RAD(0)), 2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(25), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(5), RAD(15), RAD(90)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(-5), RAD(-90)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.4, -0.6) * ANGLES(RAD(-35), RAD(90), RAD(0)) * ANGLES(RAD(-1 * SIN(SINE / 6)), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(-1 * SIN(SINE / 6)), RAD(0), RAD(0)), 1 / Animation_Speed)
		end
        Cooldown1 = true
		local LANDED = nil
		local POS = nil
		coroutine.resume(coroutine.create(function()
			repeat
				Swait()
				RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0 ) * ANGLES(RAD(20), RAD(0), RAD(0)), 1 / Animation_Speed)
				Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0 , 0 + ((1) - 1)) * ANGLES(RAD(15), RAD(0), RAD(0)), 1 / Animation_Speed)
				RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(-35 - 4 * COS(SINE / 6)), RAD(0), RAD(45 + 10 * COS(SINE / 12))) * RIGHTSHOULDERC0, 1 / Animation_Speed)
				LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(-35 - 4 * COS(SINE / 6)), RAD(0), RAD(-45 - 10 * COS(SINE / 12))) * LEFTSHOULDERC0, 1 / Animation_Speed)
				RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.3, -0.7) * ANGLES(RAD(-25 + 5 * SIN(SINE / 12)), RAD(90), RAD(0)) * ANGLES(RAD(-1 * SIN(SINE / 6)), RAD(0), RAD(0)), 1 / Animation_Speed)
				LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.8, -0.3) * ANGLES(RAD(-10), RAD(-80), RAD(0)) * ANGLES(RAD(-1 * SIN(SINE / 6)), RAD(0), RAD(0)), 1 / Animation_Speed)
			until LANDED ~= nil
		end))
		repeat
			Swait()
			local HITFLOOR,HITPOS = Raycast(RootPart.Position, (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 4.2, Character)
			if HITFLOOR then
				LANDED = HITFLOOR
				POS = HITPOS
			end
		until LANDED ~= nil
		GYRO:remove()
		Swait(4)
	for i=0, 0.5, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -2.5 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(90), RAD(0), RAD(0)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(179), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(179), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	local AMMO = 0
	repeat
	AMMO = AMMO + 1
		for i=0, 0.1, 0.05 / Animation_Speed do
			Swait()
			turnto(Mouse.Hit.p)
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -2.5 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(90), RAD(0), RAD(0)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0.35, 0 + ((1) - 1)) * ANGLES(RAD(-75 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(179), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-0.4, 0.8, 0) * ANGLES(RAD(179), RAD(0), RAD(70)) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
            ---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = RightHole.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
           ---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0, Transparency2 = 1, CFrame = RightHole.CFrame, MoveToPos = nil, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(255,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
            CreateSound(1590205662, RightArm, 7, 1, false)
            BulletDetection(RightHole.Position,Mouse.Hit.p,true)
	Humanoid.CameraOffset = VT(MRANDOM(-5,5)/2.5,MRANDOM(-5,5)/2.5,MRANDOM(-5,5)/2.5)/30
		for i=0, 0.075, 0.05 / Animation_Speed do
			Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -2.5 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(90), RAD(0), RAD(0)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0.35, 0 + ((1) - 1)) * ANGLES(RAD(-85 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(190), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-0.4, 0.8, 0) * ANGLES(RAD(190), RAD(0), RAD(70)) * LEFTSHOULDERC0, 1 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	until AMMO == 9
		ATTACK = false
		Rooted = false
wait(0.3)
Cooldown1 = false
	end
end

function Fury()
    ATTACK = true
    Rooted = false
    if EQUIPPED == false then
        Rooted = true
        for i = 0, 0.3, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, 0.01 * SIZE, -0.01 * SIZE) * ANGLES(RAD(-5), RAD(0), RAD(15)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + (1 * SIZE - 1)) * ANGLES(RAD(25 - 5 * COS(SINE / 24)), RAD(-5), RAD(-5)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(0), RAD(0), RAD(15)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(0), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, 0.2 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.5 * SIZE) * ANGLES(RAD(5), RAD(65), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(-5), RAD(-75), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
        local TOUCH = RightLeg.Touched:Connect(function(HIT)
            if HIT.Anchored == false and (HIT.Parent:FindFirstChildOfClass("Humanoid") or HIT.Parent.Parent:FindFirstChildOfClass("Humanoid")) then
                HIT:BreakJoints()
            end
        end)
        for i = 0, 0.1, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, -0.4 * SIZE, -0.1 * SIZE) * ANGLES(RAD(25), RAD(0), RAD(15)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + (1 * SIZE - 1)) * ANGLES(RAD(25 - 5 * COS(SINE / 24)), RAD(-5), RAD(-5)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(-25), RAD(0), RAD(15)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(-25), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -0.7 * SIZE, -0.5 * SIZE) * ANGLES(RAD(25), RAD(65), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1.5 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -1.1 * SIZE, -0.01 * SIZE) * ANGLES(RAD(25), RAD(-75), RAD(0)) * ANGLES(RAD(-2), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
        local HITFLOOR, HITPOS = Raycast(RightLeg.Position, CF(RootPart.Position, RootPart.Position + VT(0, -1, 0)).lookVector, 2 * SIZE, Character)
        if HITFLOOR then
            for i = 1, 5 do
                WACKYEFFECT({
                    Time = 25,
                    EffectType = "Wave",
                    Size = VT(0, 0, 5),
                    Size2 = VT(15 + i * 3, 0, 15 + i * 3),
                    Transparency = 0.7,
                    Transparency2 = 1,
                    CFrame = CF(RightLeg.CFrame * CF(0, -1.05 * SIZE, 0).p) * ANGLES(RAD(0), RAD(MRANDOM(0, 360)), RAD(0)),
                    MoveToPos = nil,
                    RotationX = 0,
                    RotationY = 0,
                    RotationZ = 0,
                    Material = "Neon",
                    Color = C3(1, 1, 1),
                    SoundID = nil,
                    SoundPitch = nil,
                    SoundVolume = nil
                })
            end
            ApplyAoE(HITPOS, 25, 35, 45, 45, false)
            CreateSound(765590102, RightLeg, 6, 1, false)
            CreateFlyingDebree(HITFLOOR, CF(HITPOS), 10, VT(2, 2, 2), 5, 75, false)
        end
        TOUCH:Disconnect()
        for i = 0, 0.2, 0.1 / Animation_Speed do
            Swait()
            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, -0.4 * SIZE, -0.1 * SIZE) * ANGLES(RAD(25), RAD(0), RAD(15)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + (1 * SIZE - 1)) * ANGLES(RAD(25 - 5 * COS(SINE / 24)), RAD(-5), RAD(-5)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(-25), RAD(0), RAD(15)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(-25), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -0.7 * SIZE, -0.5 * SIZE) * ANGLES(RAD(25), RAD(65), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1.5 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -1.1 * SIZE, -0.01 * SIZE) * ANGLES(RAD(25), RAD(-75), RAD(0)) * ANGLES(RAD(-2), RAD(0), RAD(0)), 1 / Animation_Speed)
        end
    else
        do
            local GYRO = IT("BodyGyro", RootPart)
            GYRO.D = 2
            GYRO.P = 20000
            GYRO.MaxTorque = VT(0, 4000000, 0)
            coroutine.resume(coroutine.create(function()
                repeat
                    Swait()
                    GYRO.CFrame = CF(RootPart.Position, Mouse.Hit.p)
                until ATTACK == false
                GYRO:Remove()
            end))
            local FIRING = true
            local SHOOTING = false
            local TIMER = 70
            CreateSound(1498950813, HELDGUN, 6, 1, false)
            for i = 0, 0.2, 0.1 / Animation_Speed do
                Swait()
                RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, 0 * SIZE, 0 + 0.05 * SIZE * COS(SINE / 24)) * ANGLES(RAD(0), RAD(0), RAD(35)), 1 / Animation_Speed)
                Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + (1 * SIZE - 1)) * ANGLES(RAD(15 - 5 * COS(SINE / 24)), RAD(-5), RAD(-25)), 1 / Animation_Speed)
                RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), -0.5 * SIZE) * ANGLES(RAD(80), RAD(0), RAD(25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(0), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
                LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(-45), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
            end
            local MOUSE = Mouse.Button1Down:connect(function(NEWKEY)
                if SHOOTING == false then
                    HOLD = true
                    repeat
                        SHOOTING = true
                        local GUNPOS = HELDGUN.CFrame * GUNOFFSET.p
                        local HIT, POS, NORMAL = CastProperRay(GUNPOS, Mouse.Hit.p, 1000, Character)
                        local DISTANCE = (POS - GUNPOS).Magnitude
                        if HIT then
                            if HIT.Parent:FindFirstChildOfClass("Humanoid") then
                                if HIT.Parent:FindFirstChildOfClass("Humanoid").Health > 0 then
                                    HIT.CFrame = HIT.CFrame * ANGLES(RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360)), RAD(MRANDOM(0, 360)))
                                    CreateSound(HITPLAYERSOUNDS[MRANDOM(1, #HITPLAYERSOUNDS)], HIT, 10, 1, false)
                                    ApplyDamage(HIT.Parent:FindFirstChildOfClass("Humanoid"), 99)
                                    CreateFlyingDebree(HIT, CF(POS), 7, VT(0.1, 0.1, 0.1), 5, 35, true)
                                end
                            elseif HIT.Anchored == true then
                                CreateFlyingDebree(HIT, CF(POS), 7, VT(0.2, 0.2, 0.2), 5, 35, true)
                            end
                        end
                        SHAKECAM(GUNPOS, 8, 5, 3)
                        SHAKECAM(POS, 10, 6, 6)
                        WACKYEFFECT({
                            Time = 6,
                            EffectType = "Block",
                            Size = VT(2, 2, 2),
                            Size2 = VT(4, 4, 4),
                            Transparency = 0,
                            Transparency2 = 1,
                            CFrame = CF(GUNPOS),
                            MoveToPos = nil,
                            RotationX = 0,
                            RotationY = 0,
                            RotationZ = 0,
                            Material = "Neon",
                            Color = C3(1, 1, 0),
                            SoundID = 213603013,
                            SoundPitch = 0.9,
                            SoundVolume = 10
                        })
                        for i = 0, 0.3, 0.1 / Animation_Speed do
                            Swait()
                            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, 0 * SIZE, 0 + 0.05 * SIZE * COS(SINE / 24)) * ANGLES(RAD(0), RAD(0), RAD(35)), 1 / Animation_Speed)
                            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + (1 * SIZE - 1)) * ANGLES(RAD(15 - 5 * COS(SINE / 24)), RAD(-5), RAD(-25)), 1 / Animation_Speed)
                            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), -0.5 * SIZE) * ANGLES(RAD(100), RAD(0), RAD(25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(0), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                            RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
                            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(-45), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
                        end
                        for i = 0, 0.5, 0.1 / Animation_Speed do
                            Swait()
                            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, 0 * SIZE, 0 + 0.05 * SIZE * COS(SINE / 24)) * ANGLES(RAD(0), RAD(0), RAD(35)), 1 / Animation_Speed)
                            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + (1 * SIZE - 1)) * ANGLES(RAD(15 - 5 * COS(SINE / 24)), RAD(-5), RAD(-25)), 1 / Animation_Speed)
                            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), -0.5 * SIZE) * ANGLES(RAD(80), RAD(0), RAD(25)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(0), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                            RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
                            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(-45), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
                        end
                    until HOLD == false
                    SHOOTING = false
                end
            end)
            repeat
                Swait()
                if SHOOTING == false then
                    TIMER = TIMER - 1
                    if TIMER <= 0 then
                        FIRING = false
                    end
                    RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0 * SIZE, 0 * SIZE, 0 + 0.05 * SIZE * COS(SINE / 24)) * ANGLES(RAD(0), RAD(0), RAD(35)), 1 / Animation_Speed)
                    Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + (1 * SIZE - 1)) * ANGLES(RAD(15 - 5 * COS(SINE / 24)), RAD(-5), RAD(-25)), 1 / Animation_Speed)
                    RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), -0.5 * SIZE) * ANGLES(RAD(80), RAD(0), RAD(30)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
                    LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5 * SIZE, 0.5 * SIZE - 0.05 * SIZE * SIN(SINE / 24), 0 * SIZE) * ANGLES(RAD(0), RAD(0), RAD(-15)) * LEFTSHOULDERC0, 1 / Animation_Speed)
                    RightHip.C0 = Clerp(RightHip.C0, CF(1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
                    LeftHip.C0 = Clerp(LeftHip.C0, CF(-1 * SIZE, -1 * SIZE - 0.05 * SIZE * COS(SINE / 24), -0.01 * SIZE) * ANGLES(RAD(0), RAD(-45), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 1 / Animation_Speed)
                end
            until FIRING == false and SHOOTING == false
            MOUSE:Disconnect()
        end
    end
    ATTACK = false
    Rooted = false
end

function banra()
	ATTACK = true
	Rooted = false
	for i=0, 0.4, 5 / Animation_Speed do
		Swait()
		turnto(Mouse.Hit.p)
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(90)), 0.5 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 0.5 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.6, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
	end
	repeat
		for i=0, 0.2, 5 / Animation_Speed do
			Swait()
			turnto(Mouse.Hit.p)
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(90)), 0.5 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 0.5 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(0), RAD(90)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.6, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		end
		local HIT,POS = CastProperRay(RightArm.Position, Mouse.Hit.p, 1000, Character)
		SpawnTrail(RightArm.Position,POS)
		if HIT ~= nil then
			if HIT.Parent ~= workspace and HIT.Parent.ClassName ~= "Folder" then
				Banish(HIT.Parent)
			end
		end
		---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(1,1.5,1), Transparency = 1, Transparency2 = 1, CFrame = RightArm.CFrame, MoveToPos = RightArm.CFrame*CF(0,0.5,0).p, RotationX = 0, RotationY = -15, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = nil, SoundVolume = nil})
		---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 1, Transparency2 = 1, CFrame = RightArm.CFrame, MoveToPos = nil, RotationX = 0, RotationY = 5, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = 743521450, SoundPitch = MRANDOM(8,11)/10, SoundVolume = 100000})
		---WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0.7, Transparency2 = 1, CFrame = CF(POS,RightArm.Position) * ANGLES(RAD(-90), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = -5, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = MRANDOM(8,11)/10, SoundVolume = 8})
		WACKYEFFECT({Time = 25, EffectType = "Wave", Size = VT(0.3,0,0.3), Size2 = VT(2,0.5,2), Transparency = 0.7, Transparency2 = 1, CFrame = CF(POS,RightArm.Position) * ANGLES(RAD(-90), RAD(0), RAD(0)), MoveToPos = nil, RotationX = 0, RotationY = 5, RotationZ = 0, Material = "Neon", Color = C3(0,0,0), SoundID = nil, SoundPitch = MRANDOM(8,11)/10, SoundVolume = 8})
		for i=0, 0.3, 5 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(90)), 0.5 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(-90)), 0.25 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(90), RAD(15), RAD(90)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.6, 0) * ANGLES(RAD(-45), RAD(0), RAD(45)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.5 / Animation_Speed)
		end
	until KEYHOLD == false
	ATTACK = false
	Rooted = false
end

function Punch()
	ATTACK = true
	Rooted = false
	for i=0, 0.2, 0.1 / Animation_Speed do
		Swait()
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.35, 0+ 0.15 * COS(SINE / 12), -0.2) * ANGLES(RAD(150), RAD(35), RAD(-5)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
		LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.35, 0 + 0.15 * COS(SINE / 12), -0.2) * ANGLES(RAD(130), RAD(0), RAD(5)) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
	end
	VALUE1 = true
	if COMBO == 1 then
		COMBO = 2
		CreateSound("199150686", Torso, 1, MRANDOM(11,13)/10)
		ApplyAoE3(LeftArm.Position,3,5,30,15,false)
		for i=0, 0.3, 0.1 / Animation_Speed do
			Swait()
			RootPart.CFrame = RootPart.CFrame*CF(0,0,-0.1)
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(-75)), 2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(65)), 2 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(-25), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1, 0.5, -0.5) * ANGLES(RAD(90), RAD(0), RAD(25)) * LEFTSHOULDERC0, 2 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
		end
	elseif COMBO == 2 then
		COMBO = 1
		CreateSound("199150686", Torso, 1, MRANDOM(11,13)/10)
		ApplyAoE3(RightArm.Position,3,5,30,15,false)
		for i=0, 0.3, 0.1 / Animation_Speed do
			Swait()
			RootPart.CFrame = RootPart.CFrame*CF(0,0,-0.1)
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(85)), 2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-80)), 2 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, -1) * ANGLES(RAD(90), RAD(0), RAD(-50)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(-25), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 2 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
		end
	end
	VALUE1 = false
	ATTACK = false
	Rooted = false
end

function Kick()
	ATTACK = true
	Rooted = false
		CreateSound("199150686", Torso, 1, MRANDOM(11,13)/10)
		ApplyAoE3(RightLeg.Position,3,5,30,15,false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(-25), RAD(0), RAD(45)), 2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(15), RAD(0), RAD(-45)), 2 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(12)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, -0.5) * ANGLES(RAD(25), RAD(0), RAD(45)) * LEFTSHOULDERC0, 2 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -0.8 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(45), RAD(90), RAD(0)) * ANGLES(RAD(-38), RAD(0), RAD(0)), 2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-45), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
		end
		CreateSound("199150686", Torso, 1, MRANDOM(11,13)/10)
		ApplyAoE3(LeftLeg.Position,3,5,30,15,false)
		for i=0, 0.5, 0.1 / Animation_Speed do
			Swait()
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 12)) * ANGLES(RAD(-25), RAD(0), RAD(-45)), 2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(15), RAD(0), RAD(45)), 2 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, -0.5) * ANGLES(RAD(25), RAD(0), RAD(-45)) * RIGHTSHOULDERC0, 2 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-12)) * LEFTSHOULDERC0, 2 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(45), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -0.8 - 0.05 * COS(SINE / 12), -0.01) * ANGLES(RAD(45), RAD(-90), RAD(0)) * ANGLES(RAD(-38), RAD(0), RAD(0)), 2 / Animation_Speed)
		end
	ATTACK = false
	Rooted = false
end
--//=================================\\
--||	  ASSIGN THINGS TO KEYS
--\\=================================//

function MouseDown(Mouse)
	HOLD = true
	if ATTACK == false then
	end
end

function MouseUp(Mouse)
HOLD = false
end

function KeyDown(Key)
	KEYHOLD = true
	if Key == "z" and ATTACK == false then
		if MODE == "Killbot" then
			Bullet()
		elseif MODE == "GoodCop" then
			GoldenPunch()
		elseif MODE == "DeadEye" then
			CORRUPTEDLETHALBULLETS2()
		elseif MODE == "Superior" then
			ManiacMissiles()
        elseif MODE == "black" then
			MagicMissiles()
		end
	end

	if Key == "x" and ATTACK == false then
		if MODE == "black" then
			Warp()
		elseif MODE == "GoodCop" then
			ImplosionCollision()
		elseif MODE == "Superior" then
			Prison_Key()
		elseif MODE == "DeadEye" then
			BulletRoller()
	end
	end

	if Key == "c" and ATTACK == false then
		if MODE == "black" then
		Corrupted_Burn()
		elseif MODE == "DeadEye" then
			Teleport()
       elseif MODE == "GoodCop" then
			Mach20()
	end
	end

	if Key == "v" and ATTACK == false then
		if MODE == "GoodCopt" then
		
		elseif MODE == "black" then
		PlanetaryDevastation()	
	end
	end

	if Key == "t" and ATTACK == false then
		if MODE == "Killbot" then
			nope()
		elseif MODE == "GoodCop" then
			TomYumTaunt()
		elseif MODE == "DeadEye" then
			Taunt()
		elseif MODE == "black" then
			VoidedTaunt()
		end
	end

	if Key == "q" and ATTACK == false then
		Punch()
	end

	if Key == "e" and ATTACK == false then
		Kick()
	end

	if Key == "f" and ATTACK == false then
		banra()
	end

	if Key == "p" and ATTACK == false then
		Switch()
	end

   if Key == "l" and ATTACK == false then
		Switch4()
	end

	if Key == "u" and ATTACK == false then
		Switch2()
	end

	if Key == "y" and ATTACK == false then
		Switch3()
	end

end

function KeyUp(Key)
	KEYHOLD = false
end

	Mouse.Button1Down:connect(function(NEWKEY)
		MouseDown(NEWKEY)
	end)
	Mouse.Button1Up:connect(function(NEWKEY)
		MouseUp(NEWKEY)
	end)
	Mouse.KeyDown:connect(function(NEWKEY)
		KeyDown(NEWKEY)
	end)
	Mouse.KeyUp:connect(function(NEWKEY)
		KeyUp(NEWKEY)
	end)

--//=================================\\
--\\=================================//


function unanchor()
	if UNANCHOR == true then
		g = Character:GetChildren()
		for i = 1, #g do
			if g[i].ClassName == "Part" then
				g[i].Anchored = false
			end
		end
	end
end


--//=================================\\
--||	WRAP THE WHOLE SCRIPT UP
--\\=================================//

Humanoid.Changed:connect(function(Jump)
	if Jump == "Jump" and (Disable_Jump == true) then
		Humanoid.Jump = false
	end
end)

while true do
	Swait()
	script.Parent = WEAPONGUI
	for _,v in next, Humanoid:GetPlayingAnimationTracks() do
	    v:Stop();
	end
	ANIMATE.Parent = nil
	SINE = SINE + CHANGE*1.5
	local TORSOVELOCITY = (RootPart.Velocity * VT(1, 0, 1)).magnitude
	local TORSOVERTICALVELOCITY = RootPart.Velocity.y
	local HITFLOOR = Raycast(RootPart.Position, (CF(RootPart.Position, RootPart.Position + VT(0, -1, 0))).lookVector, 4*SIZE, Character)
	local WALKSPEEDVALUE = 10 / (Humanoid.WalkSpeed / 16)
	if ANIM == "Walk" and TORSOVELOCITY > 1 then
		if MODE == "GoodCop" then
		 RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, 0.1 * COS(SINE / (WALKSPEEDVALUE/2))) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
    Neck.C1 = Clerp(Neck.C1, CF(0, -0.5, 0) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(0), RAD(0), RAD(0) - Head.RotVelocity.Y / 30), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
    RightHip.C1 = Clerp(RightHip.C1, CF(0.5, 0.875 - 0.5 * SIN(SINE / WALKSPEEDVALUE) - 0.15 * COS(SINE / WALKSPEEDVALUE*2), 0.3 * SIN(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(10+50 * COS(SINE / WALKSPEEDVALUE))), 0.6 / Animation_Speed)
    LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5, 0.875 + 0.5 * SIN(SINE / WALKSPEEDVALUE) - 0.15 * COS(SINE / WALKSPEEDVALUE*2), -0.3 * SIN(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-10+50 * COS(SINE / WALKSPEEDVALUE))), 0.6 / Animation_Speed)
		elseif MODE == "Superior" then
RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, -0.15 * COS(SINE / (WALKSPEEDVALUE / 2))) * ANGLES(RAD(0), RAD(0) - RootPart.RotVelocity.Y / 75, RAD(0)), 2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
		Neck.C1 = Clerp(Neck.C1, CF(0, -0.5, 0) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(2.5 * SIN(SINE / (WALKSPEEDVALUE / 2))), RAD(0), RAD(0) - Head.RotVelocity.Y / 30), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
		RightHip.C1 = Clerp(RightHip.C1, CF(0.5*SIZE, 0.875*SIZE - 0.125 * SIN(SINE / WALKSPEEDVALUE)*SIZE - 0.15 * COS(SINE / WALKSPEEDVALUE*2), 0 +0.5+ 0.2 * COS(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0) - RightLeg.RotVelocity.Y / 75, RAD(0), RAD(25+80 * COS(SINE / WALKSPEEDVALUE))), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
		LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5*SIZE, 0.875*SIZE + 0.125 * SIN(SINE / WALKSPEEDVALUE)*SIZE - 0.15 * COS(SINE / WALKSPEEDVALUE*2), 0 +0.5+ -0.2 * COS(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0) + LeftLeg.RotVelocity.Y / 75, RAD(0), RAD(-25+80 * COS(SINE / WALKSPEEDVALUE))), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
       elseif MODE == "Killbot" then
   RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, 0.1 * COS(SINE / (WALKSPEEDVALUE/2))) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
        Neck.C1 = Clerp(Neck.C1, CF(0, -0.5, 0) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(0), RAD(0), RAD(0) - Head.RotVelocity.Y / 30), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
        RightHip.C1 = Clerp(RightHip.C1, CF(0.5, 0.875 - 0.125 * SIN(SINE / WALKSPEEDVALUE) - 0.15 * COS(SINE / WALKSPEEDVALUE*2), 0.25 * SIN(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(10+50 * COS(SINE / WALKSPEEDVALUE))), 0.6 / Animation_Speed)
        LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5, 0.875 + 0.125 * SIN(SINE / WALKSPEEDVALUE) - 0.15 * COS(SINE / WALKSPEEDVALUE*2), -0.25 * SIN(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-10+50 * COS(SINE / WALKSPEEDVALUE))), 0.6 / Animation_Speed)
		elseif MODE == "DeadEye" then
		RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, -0.15 * COS(SINE / (WALKSPEEDVALUE / 2)) * Player_Size) * ANGLES(RAD(0), RAD(0) - RootPart.RotVelocity.Y / 75, RAD(0)), 2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
        Neck.C1 = Clerp(Neck.C1, CF(0 * Player_Size, -0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(2.5 * SIN(SINE / (WALKSPEEDVALUE / 2))), RAD(0), RAD(0) - Head.RotVelocity.Y / 30), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
        RightHip.C1 = Clerp(RightHip.C1, CF(0.5 * Player_Size, 0.875 * Player_Size - 0.125 * SIN(SINE / WALKSPEEDVALUE) * Player_Size, -0.125 * COS(SINE / WALKSPEEDVALUE) * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0) - RightLeg.RotVelocity.Y / 75, RAD(0), RAD(76 * COS(SINE / WALKSPEEDVALUE))), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
        LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5 * Player_Size, 0.875 * Player_Size + 0.125 * SIN(SINE / WALKSPEEDVALUE) * Player_Size, 0.125 * COS(SINE / WALKSPEEDVALUE) * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0) + LeftLeg.RotVelocity.Y / 75, RAD(0), RAD(76 * COS(SINE / WALKSPEEDVALUE))), 0.2 * (Humanoid.WalkSpeed / 16) / Animation_Speed)
		end
	elseif (ANIM ~= "Walk") or (TORSOVELOCITY < 1) then
        if MODE == "GoodCop" then
		 RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
    Neck.C1 = Clerp(Neck.C1, CF(0, -0.5, 0) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
    RightHip.C1 = Clerp(RightHip.C1, CF(0.5, 1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.7 / Animation_Speed)
    LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5, 1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.7 / Animation_Speed)
        elseif MODE == "Superior" then
  RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
		Neck.C1 = Clerp(Neck.C1, CF(0, -0.5, 0) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		RightHip.C1 = Clerp(RightHip.C1, CF(0.5*SIZE, 1*SIZE, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5*SIZE, 1*SIZE, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
       elseif MODE == "Killbot" then
   RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
        Neck.C1 = Clerp(Neck.C1, CF(0, -0.5, 0) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
        RightHip.C1 = Clerp(RightHip.C1, CF(0.5, 1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.7 / Animation_Speed)
        LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5, 1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.7 / Animation_Speed)
        elseif MODE == "DeadEye" then
	  RootJoint.C1 = Clerp(RootJoint.C1, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
        Neck.C1 = Clerp(Neck.C1, CF(0 * Player_Size, -0.5 * Player_Size, 0 * Player_Size) * ANGLES(RAD(-90), RAD(0), RAD(180)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
        RightHip.C1 = Clerp(RightHip.C1, CF(0.5 * Player_Size, 1 * Player_Size, 0 * Player_Size) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
        LeftHip.C1 = Clerp(LeftHip.C1, CF(-0.5 * Player_Size, 1 * Player_Size, 0 * Player_Size) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)

	end
end
	if TORSOVERTICALVELOCITY > 1 and HITFLOOR == nil then
		ANIM = "Jump"
			  if ATTACK == false then
            RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0*SIZE, 0*SIZE, 0*SIZE) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0*SIZE, 0*SIZE, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(-20), RAD(0), RAD(0)), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE, 0*SIZE) * ANGLES(RAD(-40), RAD(0), RAD(20)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.5*SIZE, 0*SIZE) * ANGLES(RAD(-40), RAD(0), RAD(-20)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1*SIZE, -0.3*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(-20)), 1 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE, -0.3*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-5), RAD(0), RAD(20)), 1 / Animation_Speed)
        end
	elseif TORSOVERTICALVELOCITY < -1 and HITFLOOR == nil then
		ANIM = "Fall"
		if ATTACK == false then
			RootJoint.C0 = Clerp(RootJoint.C0, ROOTC0 * CF(0, 0, 0) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.2 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0 , 0 + ((1) - 1)) * ANGLES(RAD(20), RAD(0), RAD(0)), 0.2 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(60)) * RIGHTSHOULDERC0, 0.2 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(0), RAD(0), RAD(-60)) * LEFTSHOULDERC0, 0.2 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1, -1, 0) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(20)), 0.2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(10)), 0.2 / Animation_Speed)
		end
	elseif TORSOVELOCITY < 1 and HITFLOOR ~= nil then
		ANIM = "Idle"
		if ATTACK == false then
			if MODE == "GoodCop" then
			Speed = 20
			   RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 - 0.1 * COS(SINE / 12)) * ANGLES(RAD(5 * COS(SINE / 12)), RAD(0), RAD(0)), 0.5 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(7 * COS(SINE / 12)), RAD(0)), 0.5 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1, 0.5 - 0.1 * COS(SINE / 12), -0.5) * ANGLES(RAD(0), RAD(0), RAD(-100)) * ANGLES(RAD(20), RAD(0), RAD(0)) * RIGHTSHOULDERC0, 0.5 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1, 0.3 - 0.1 * COS(SINE / 12), -0.35) *  ANGLES(RAD(0), RAD(0), RAD(70)) * ANGLES(RAD(20), RAD(0), RAD(0)) * LEFTSHOULDERC0, 0.5 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 + 0.1 * COS(SINE / 12), -0.01) * ANGLES(RAD(7 * COS(SINE / 12)), RAD(85), RAD(0)) * ANGLES(RAD(-6), RAD(0), RAD(0)), 0.5 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 + 0.1 * COS(SINE / 12), -0.01) * ANGLES(RAD(7 * COS(SINE / 12)), RAD(-85), RAD(0)) * ANGLES(RAD(-6), RAD(0), RAD(0)), 0.5 / Animation_Speed)
			elseif MODE == "Superior" then
			Speed = 10
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0*SIZE, 0*SIZE, 0 + 0.05*SIZE * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(15)) * ANGLES(RAD(15), RAD(0), RAD(0)), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1*SIZE) - 1)) * ANGLES(RAD(0 - 2.5 * SIN(SINE / 12)), RAD(0), RAD(-15)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.65*SIZE, 0*SIZE) * ANGLES(RAD(45), RAD(0), RAD(52 - 3 * COS(SINE / 12))) * ANGLES(RAD(0), RAD(-70), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.65*SIZE, 0*SIZE) * ANGLES(RAD(35), RAD(0), RAD(-42 + 3 * COS(SINE / 12))) * ANGLES(RAD(0), RAD(70), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -1.02*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(20), RAD(55), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(0)), 1 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(10), RAD(-74), RAD(0)) * ANGLES(RAD(-3), RAD(0), RAD(0)), 1 / Animation_Speed)
			if MRANDOM(1,35) == 1 then
				Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1*2) - 1)) * ANGLES(RAD(MRANDOM(-15,15)), RAD(MRANDOM(-15,15)), RAD(MRANDOM(-15,15))), 1)
			end
           elseif MODE == "Killbot" then
			Speed = 10
		RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 + 0.05 * COS(SINE / 4)) * ANGLES(RAD(0), RAD(0), RAD(0)), 1 / Animation_Speed)
		Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 - 0.1 * COS(SINE / 3) + ((1) - 1)) * ANGLES(RAD(30 - 2.5 * SIN(SINE / 12)), RAD(7 * COS(SINE / 24)), RAD(0)), 0.5 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.1, 0.4 - 0.1 * COS(SINE / 4), 0.25 - 0.025 * COS(SINE / 5)) * ANGLES(RAD(-30), RAD(0), RAD(-30)) * RIGHTSHOULDERC0, 0.4 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.1, 0.4 - 0.1 * COS(SINE / 4), 0.25 - 0.025 * COS(SINE / 5)) * ANGLES(RAD(-30), RAD(0), RAD(30)) * LEFTSHOULDERC0, 0.4 / Animation_Speed)
		RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 - 0.05 * COS(SINE / 4), -0.01) * ANGLES(RAD(0), RAD(80), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
		LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.05 * COS(SINE / 4), -0.01) * ANGLES(RAD(0), RAD(-80), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 1 / Animation_Speed)
			elseif MODE == "DeadEye" then
			Speed = 12
			  RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 0 - 0.15 * COS(SINE / 12)) * ANGLES(RAD(0), RAD(0), RAD(0)), 0.15 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 - 0.1 * COS(SINE / 12) + ((1) - 1)) * ANGLES(RAD(15 - 2.5 * SIN(SINE / 12)), RAD(10 * COS(SINE / 12)), RAD(0)), 0.15 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.2, 0.5 - 0.15 * COS(SINE / 12), -0.4) * ANGLES(RAD(50 - 10.5 * SIN(SINE / 12)), RAD(0 - 3.5 * SIN(SINE / 12)), RAD(-36)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
        LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5 - 0.15 * COS(SINE / 12), 0) * ANGLES(RAD(180 - 3.5 * SIN(SINE / 12)), RAD(0 - 8.5 * SIN(SINE / 12)), RAD(20 + 8.5 * SIN(SINE / 12))) * LEFTSHOULDERC0, 0.15 / Animation_Speed)
        RightHip.C0 = Clerp(RightHip.C0, CF(1, -1 + 0.15 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 0.15 / Animation_Speed)
        LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 + 0.15 * COS(SINE / 12), -0.01) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-4), RAD(0), RAD(0)), 0.15 / Animation_Speed)
			elseif MODE == "black" then
			Speed = 16
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 1 + 0.5 * COS(SINE / 12)) * ANGLES(RAD(-25 - 3 * SIN(SINE / 12)), RAD(0), RAD(-25)), 0.15 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(23 - 3 * SIN(SINE / 12)), RAD(0), RAD(30)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(0.75*SIZE, 0.5*SIZE, -1*SIZE) * ANGLES(RAD(70), RAD(0), RAD(-70)) * ANGLES(RAD(20), RAD(25), RAD(-15)) * RIGHTSHOULDERC0, 0.4 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1*SIZE, 0.2*SIZE, -0.5*SIZE) * ANGLES(RAD(25), RAD(0), RAD(85)) * LEFTSHOULDERC0, 0.4 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -0.5*SIZE - 0.05*SIZE * COS(SINE / 12), -0.5*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -0.8*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
			end
		end
	elseif TORSOVELOCITY > 1 and HITFLOOR ~= nil then
		ANIM = "Walk"
		if ATTACK == false then
			if MODE == "GoodCop" then
			Speed = 26
		 RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.05) * ANGLES(RAD(15), RAD(0), RAD(-7 * COS(SINE / (WALKSPEEDVALUE)))), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5 - 1 * SIN(SINE / (WALKSPEEDVALUE / 2))), RAD(0), RAD(7 * COS(SINE / (WALKSPEEDVALUE)))), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(50 * COS(SINE / (WALKSPEEDVALUE))), RAD(-5), RAD(-30 * COS(SINE / (WALKSPEEDVALUE)))) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(-50 * COS(SINE / (WALKSPEEDVALUE))), RAD(5), RAD(-30 * COS(SINE / (WALKSPEEDVALUE)))) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1 , -1, 0) * ANGLES(RAD(0), RAD(85), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
			elseif MODE == "Superior" then
			Speed = 16
RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0*SIZE, 0*SIZE, -0.1*SIZE) * ANGLES(RAD(15), RAD(0), RAD(-8*SIN(SINE/WALKSPEEDVALUE))), 1 / Animation_Speed)
			Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1*SIZE) - 1)) * ANGLES(RAD(5), RAD(-2*SIN(SINE/WALKSPEEDVALUE)), RAD(8*SIN(SINE/WALKSPEEDVALUE))), 1/ Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5*SIZE, 0.5*SIZE + 0.15 * COS(SINE/WALKSPEEDVALUE*2), 0.1*SIZE-0.3*SIN(SINE/WALKSPEEDVALUE)) * ANGLES(RAD(35), RAD(0), RAD(22 + 3* SIN(SINE/WALKSPEEDVALUE))) * ANGLES(RAD(0), RAD(-70), RAD(0)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5*SIZE, 0.5*SIZE + 0.15 * COS(SINE/WALKSPEEDVALUE*2), 0.1*SIZE+0.3*SIN(SINE/WALKSPEEDVALUE)) * ANGLES(RAD(35), RAD(0), RAD(-22 - 3* SIN(SINE/WALKSPEEDVALUE))) * ANGLES(RAD(0), RAD(70), RAD(0)) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE , -1*SIZE, 0*SIZE) * ANGLES(RAD(0), RAD(75), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-15)), 2 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -1*SIZE, 0*SIZE) * ANGLES(RAD(0), RAD(-75), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(15)), 2 / Animation_Speed)
            elseif MODE == "Killbot" then
			Speed = 30
  RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.05) * ANGLES(RAD(15), RAD(0), RAD(-7 * COS(SINE / (WALKSPEEDVALUE)))), 1 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5 - 1 * SIN(SINE / (WALKSPEEDVALUE / 2))), RAD(0), RAD(7 * COS(SINE / (WALKSPEEDVALUE)))), 1 / Animation_Speed)
            RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(-15), RAD(-5), RAD(5)) * RIGHTSHOULDERC0, 1 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(-15), RAD(5), RAD(-5)) * LEFTSHOULDERC0, 1 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1 , -1, 0) * ANGLES(RAD(0), RAD(85), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1, 0) * ANGLES(RAD(0), RAD(-85), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(0)), 2 / Animation_Speed)
			elseif MODE == "DeadEye" then
			Speed = 16
			 RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, -0.05) * ANGLES(RAD(5), RAD(0), RAD(-15-7 * COS(SINE / (WALKSPEEDVALUE)))), 0.15 / Animation_Speed)
            Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1) - 1)) * ANGLES(RAD(5 - 1 * SIN(SINE / (WALKSPEEDVALUE / 2))), RAD(0), RAD(15+7 * COS(SINE / (WALKSPEEDVALUE)))), 0.15 / Animation_Speed)
        RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.5, 0.5, 0) * ANGLES(RAD(0 - 3.5 * SIN(SINE / 12)), RAD(0 - 3.5 * SIN(SINE / 12)), RAD(0)) * RIGHTSHOULDERC0, 0.15 / Animation_Speed)
            LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.5, 0.5, 0) * ANGLES(RAD(3 - 3.5 * SIN(SINE / 12)), RAD(0), RAD(-25 - 6 * SIN(SINE / 12))) * LEFTSHOULDERC0, 0.2 / Animation_Speed)
            RightHip.C0 = Clerp(RightHip.C0, CF(1 , -1 - 0.15 * COS(SINE / WALKSPEEDVALUE*2), -0.2+ 0.2 * COS(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(-15)), 2 / Animation_Speed)
            LeftHip.C0 = Clerp(LeftHip.C0, CF(-1, -1 - 0.15 * COS(SINE / WALKSPEEDVALUE*2), -0.2+ -0.2 * COS(SINE / WALKSPEEDVALUE)) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(0), RAD(0), RAD(15)), 2 / Animation_Speed)
			elseif MODE == "black" then
			Speed = 16
			RootJoint.C0 = Clerp(RootJoint.C0,ROOTC0 * CF(0, 0, 1 + 0.5 * COS(SINE / 12)) * ANGLES(RAD(35), RAD(0), RAD(0)), 0.25 / Animation_Speed)
		    Neck.C0 = Clerp(Neck.C0, NECKC0 * CF(0, 0, 0 + ((1.1*SIZE) - 1)) * ANGLES(RAD(-25), RAD(0), RAD(0)), 1 / Animation_Speed)
			RightShoulder.C0 = Clerp(RightShoulder.C0, CF(1.25*SIZE, 0.6*SIZE, -0.35*SIZE) * ANGLES(RAD(175), RAD(0 + 2.5 * SIN(SINE / WALKSPEEDVALUE)), RAD(-35 + 2.5 * SIN(SINE / WALKSPEEDVALUE))) * RIGHTSHOULDERC0, 1 / Animation_Speed)
			LeftShoulder.C0 = Clerp(LeftShoulder.C0, CF(-1.25*SIZE, 0.5*SIZE, 0.5*SIZE) * ANGLES(RAD(-35), RAD(25 - 2.5 * SIN(SINE / WALKSPEEDVALUE)), RAD(55 - 2.5 * SIN(SINE / WALKSPEEDVALUE))) * LEFTSHOULDERC0, 1 / Animation_Speed)
			RightHip.C0 = Clerp(RightHip.C0, CF(1*SIZE, -0.5*SIZE - 0.05*SIZE * COS(SINE / 12), -0.5*SIZE) * ANGLES(RAD(0), RAD(90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
			LeftHip.C0 = Clerp(LeftHip.C0, CF(-1*SIZE, -0.8*SIZE - 0.05*SIZE * COS(SINE / 12), -0.01*SIZE) * ANGLES(RAD(0), RAD(-90), RAD(0)) * ANGLES(RAD(-8), RAD(0), RAD(0)), 0.15 / Animation_Speed)
			end
		end
	end
	unanchor()
	Humanoid.MaxHealth = "inf"
	Humanoid.Health = "inf"
	if Rooted == false then
		Disable_Jump = false
		Humanoid.WalkSpeed = Speed
	elseif Rooted == true then
		Disable_Jump = true
		Humanoid.WalkSpeed = 0
	end
	refit()
   local MATHS = {"a","o","m","F","e","t","t","y"}
	Humanoid.Name = MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]..MATHS[MRANDOM(1,#MATHS)]
	if Character:FindFirstChildOfClass("Accessory") then
		Character:FindFirstChildOfClass("Accessory"):remove()
	elseif Character:FindFirstChildOfClass("Hat") then
		Character:FindFirstChildOfClass("Hat"):remove()
	end
	if Head:FindFirstChild("face") then
		Head.face.Texture = "rbxassetid://403870689"
	end
	for _, c in pairs(Character:GetChildren()) do
		if c.ClassName == "Part" and c.Name ~= "Eye" then
			c.Color = C3(21,71,109)
			c.Material = "Neon"
			if c:FindFirstChildOfClass("ParticleEmitter") then
				c:FindFirstChildOfClass("ParticleEmitter"):remove()
			end
		elseif (c.ClassName == "Shirt" or c.ClassName == "Pants") and c.Name ~= "Cloth" then
			c:remove()
		end
	end
end

--//=================================\\
--\\=================================//





--//====================================================\\--
--||			  		 END OF SCRIPT
--\\====================================================//--
