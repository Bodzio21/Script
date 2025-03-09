local a=game:GetService"UserInputService"
local b=game.workspace.CurrentCamera
game:GetService"TweenService"
local c=game:GetService"Players"
game:GetService"RunService"
local d=game:GetService"CoreGui"
local e=game:GetService"RunService"
local f=500
local g=Color3.fromRGB(0,255,0)

local h={}
local i={}


local j={
ChamsEnabled=false,
InnerTransparency=1,
TeamCheck=false,
UseTeamColor=false,
ShowNames=false
}-- ESP Settings



local k={
enabled=true,
boxes=false,
names=false,
tracers=false,
health=false,
teamColor=false,
showDistance=false,
showHealthNumber=false,
maxDistance=1000,
boxElements={},
nameElements={},
tracerElements={},
healthElements={},
skeletonElements={},
settings={
textSize=8,
boxThickness=1,
tracerThickness=1,
transparency=1,
},
colors={
box=Color3.fromRGB(255,255,255),
tracer=Color3.fromRGB(255,255,255),
health=Color3.fromRGB(0,255,0),
text=Color3.fromRGB(255,255,255)
}
}-- Cache frequently used values


local l=Vector2.new local m=
Vector3.new
local n=Color3.new
local o=math.floor
local p=string.format local q=
table.remove


local r=false
local s

local t=false
local u=3-- Aimbot Variables


local v=1
local w={
aim=false,
aimbotEnabled=false
}
local x=c.LocalPlayer-- Load Rayfield





local y=loadstring(game:HttpGet'https://sirius.menu/rayfield')()

setclipboard"https://discord.gg/xX5SVJFJYA"

local z=y:CreateWindow{
Name="Counter Blox Overhaul by Morro v1.00",
Icon=0,
LoadingTitle="Loading...",
LoadingSubtitle="by Morro",
Theme="Amethyst",
DisableRayfieldPrompts=false,
DisableBuildWarnings=false,
ConfigurationSaving={
Enabled=true,
FolderName=nil,
FileName="Morro"
},
Discord={
Enabled=false,
Invite="https://discord.gg/2pQ9KRTS2y",
RememberJoins=true
},
KeySystem=true,
KeySettings={
Title="Counter Blox: Reforged v1.00",
Subtitle="By Morro'",
Note="Discord link has been copied to your clipboard!",
FileName="Key",
SaveKey=false,
GrabKeyFromSite=true,
Key={"https://pastebin.com/raw/9KraP7ku"}
}
}-- Optimized distance format function




local function formatDistance(A)
return p("%.0f",A)
end-- Cleanup all ESP function


local function cleanupAllESP()
for A,B in ipairs(c:GetPlayers())do
if k.boxElements[B]then
k.boxElements[B].Visible=false
k.nameElements[B].Visible=false
k.tracerElements[B].Visible=false

local C=k.healthElements[B]
if C then
C.background.Visible=false
C.fill.Visible=false
C.outline.Visible=false
C.text.Visible=false
end

end
end
end-- Optimized cleanup function

local function cleanupESPElement(A)
local B=k.boxElements[A]
if B then
k.boxElements[A]:Remove()
k.nameElements[A]:Remove()
k.tracerElements[A]:Remove()

local C=k.healthElements[A]
if C then
C.background:Remove()
C.fill:Remove()
C.outline:Remove()
C.text:Remove()
end

k.boxElements[A]=nil
k.nameElements[A]=nil
k.tracerElements[A]=nil
k.healthElements[A]=nil
end
end-- Optimized ESP element creation


local function createESPElement(A)
if not k.boxElements[A]then-- Box ESP

local B=Drawing.new"Square"
B.Thickness=k.settings.boxThickness
B.Filled=false
B.Transparency=k.settings.transparency
k.boxElements[A]=B-- Name ESP


local C=Drawing.new"Text"
C.Size=k.settings.textSize
C.Center=true
C.Outline=true
C.Transparency=k.settings.transparency
k.nameElements[A]=C-- Tracer ESP


local D=Drawing.new"Line"
D.Thickness=k.settings.tracerThickness
D.Transparency=k.settings.transparency
k.tracerElements[A]=D-- Health ESP


local E={
background=Drawing.new"Square",
fill=Drawing.new"Square",
outline=Drawing.new"Square",
text=Drawing.new"Text"
}

E.background.Filled=true
E.background.Color=n(0,0,0)
E.background.Transparency=k.settings.transparency

E.fill.Filled=true
E.fill.Transparency=k.settings.transparency

E.outline.Thickness=1
E.outline.Filled=false
E.outline.Color=n(0,0,0)
E.outline.Transparency=k.settings.transparency

E.text.Size=k.settings.textSize
E.text.Center=true
E.text.Outline=true
E.text.Transparency=k.settings.transparency

k.healthElements[A]=E
end
end-- Optimized update function


local function updateESP()
if not k.enabled then
cleanupAllESP()
return
end

for A,B in ipairs(c:GetPlayers())do local C=false repeat
if B==x then C=true break end

local D=B.Character
local E=D and D:FindFirstChild"HumanoidRootPart"
local F=D and D:FindFirstChild"Humanoid"

if not(D and E and F and F.Health>0)then
if k.boxElements[B]then
k.boxElements[B].Visible=false
k.nameElements[B].Visible=false
k.tracerElements[B].Visible=false
local G=k.healthElements[B]
if G then
G.background.Visible=false
G.fill.Visible=false
G.outline.Visible=false
G.text.Visible=false
end
end C=true
break
end

local G=(b.CFrame.Position-E.Position).Magnitude
if G>k.maxDistance then
if k.boxElements[B]then
k.boxElements[B].Visible=false
k.nameElements[B].Visible=false
k.tracerElements[B].Visible=false
local H=k.healthElements[B]
if H then
H.background.Visible=false
H.fill.Visible=false
H.outline.Visible=false
H.text.Visible=false
end
end C=true
break
end

local H,I=b:WorldToViewportPoint(E.Position)

if I then
createESPElement(B)

local J=k.teamColor and B.TeamColor.Color or k.colors.box
local K=l(2000/H.Z,2500/H.Z)
local L=l(H.X-K.X/2,H.Y-K.Y/2)-- Box ESP


if k.boxes then
local M=k.boxElements[B]
M.Size=K
M.Position=L
M.Color=J
M.Visible=true
else
k.boxElements[B].Visible=false
end-- Name ESP (Optimized)


if k.names then
local M=k.nameElements[B]
if k.showDistance then
M.Text=B.Name..' ['..formatDistance(G)..'m]'
else
M.Text=B.Name
end
M.Position=l(H.X,L.Y-15)
M.Color=k.colors.text
M.Visible=true
else
k.nameElements[B].Visible=false
end-- Tracer ESP


if k.tracers then
local M=k.tracerElements[B]
M.From=l(b.ViewportSize.X/2,b.ViewportSize.Y)
M.To=l(H.X,H.Y)
M.Color=J
M.Visible=true
else
k.tracerElements[B].Visible=false
end-- Health ESP


if k.health then
local M=F.Health/F.MaxHealth
local N=k.healthElements[B]
local O=4

N.background.Size=l(O,K.Y)
N.background.Position=l(L.X-O-2,L.Y)
N.background.Visible=true

N.fill.Size=l(O,K.Y*M)
N.fill.Position=l(L.X-O-2,L.Y+K.Y*(1-M))
N.fill.Color=n(1-M,M,0)
N.fill.Visible=true

N.outline.Size=l(O,K.Y)
N.outline.Position=l(L.X-O-2,L.Y)
N.outline.Visible=true

if k.showHealthNumber then
N.text.Text=o(F.Health)
N.text.Position=l(L.X-O-2,L.Y-15)
N.text.Color=n(1-M,M,0)
N.text.Visible=true
else
N.text.Visible=false
end
else
local M=k.healthElements[B]
M.background.Visible=false
M.fill.Visible=false
M.outline.Visible=false
M.text.Visible=false
end
else
if k.boxElements[B]then
k.boxElements[B].Visible=false
k.nameElements[B].Visible=false
k.tracerElements[B].Visible=false
local J=k.healthElements[B]
J.background.Visible=false
J.fill.Visible=false
J.outline.Visible=false
J.text.Visible=false
end
end C=true until true if not C then break end
end
end








local function ShouldHighlight(A)
return A and(A~=x)and j.ChamsEnabled and(not j.TeamCheck or(A.Team~=x.Team))
end

local function CreateNameTag(A)
if not j.ShowNames or i[A]then return end

local B=A.Character
if not B then return end

local C=B:FindFirstChild"Head"
if not C then return end

if i[A]then
i[A]:Destroy()
i[A]=nil
end

local D=Instance.new"BillboardGui"
D.Name="NameESP"
D.Size=UDim2.new(0,200,0,50)
D.StudsOffset=Vector3.new(0,2,0)
D.AlwaysOnTop=true
D.LightInfluence=0
D.MaxDistance=f
D.Adornee=C

local E=Instance.new"TextLabel"
E.Size=UDim2.new(1,0,0,20)
E.BackgroundTransparency=1
E.TextColor3=Color3.new(1,1,1)
E.TextStrokeTransparency=0
E.TextStrokeColor3=Color3.new(0,0,0)
E.Font=Enum.Font.GothamBold
E.TextSize=14
E.Text=A.Name
E.Parent=D

D.Parent=d
i[A]=D
end

local function RemoveNameTag(A)
if i[A]then
i[A]:Destroy()
i[A]=nil
end
end

local function HighlightPlayer(A)
if not A or not A.Character then return end

if h[A]then
h[A]:Destroy()
h[A]=nil
end

local B=Instance.new"Highlight"
B.FillColor=(j.UseTeamColor and A.Team and A.Team.TeamColor.Color)or g
B.FillTransparency=j.InnerTransparency
B.OutlineColor=B.FillColor
B.OutlineTransparency=0
B.Parent=A.Character

h[A]=B

if j.ShowNames then
CreateNameTag(A)
end
end

local function RemoveHighlight(A)
if h[A]then
h[A]:Destroy()
h[A]=nil
RemoveNameTag(A)
end
end

local function UpdateHighlight(A)
if not h[A]then return end

local B=h[A]
B.FillColor=(j.UseTeamColor and A.Team and A.Team.TeamColor.Color)or g
B.OutlineColor=B.FillColor
B.FillTransparency=j.InnerTransparency
end

local function UpdatePlayer(A)
if ShouldHighlight(A)then
HighlightPlayer(A)
else
RemoveHighlight(A)
end
end

local function UpdateAllPlayers()
for A,B in ipairs(c:GetPlayers())do
UpdatePlayer(B)
end
end

local function SetupPlayer(A)
if not A or(A==LocalPlayer)then return end

A.CharacterAdded:Connect(function()
task.wait(0.1)
UpdatePlayer(A)
end)

A.CharacterRemoving:Connect(function()
RemoveHighlight(A)
end)
end

for A,B in ipairs(c:GetPlayers())do
SetupPlayer(B)
if B.Character then
UpdatePlayer(B)
end
end

c.PlayerAdded:Connect(function(A)
SetupPlayer(A)
end)

c.PlayerRemoving:Connect(function(A)
RemoveHighlight(A)
end)

e.Heartbeat:Connect(function()
if not j.ChamsEnabled then return end

for A,B in ipairs(c:GetPlayers())do local C=false repeat
if B==LocalPlayer then C=true break end

if ShouldHighlight(B)then
if not h[B]or not h[B].Parent then
UpdatePlayer(B)
end
else
RemoveHighlight(B)
end C=true until true if not C then break end
end
end)-- Functions




local function getClosestEnemy()
local A
local B=math.huge

for C,D in pairs(c:GetPlayers())do
if D~=x and D.Team~=x.Team and D.Character and D.Character:FindFirstChild"HumanoidRootPart"then
local E=(x.Character.HumanoidRootPart.Position-D.Character.HumanoidRootPart.Position).magnitude
if E<B then
A=D
B=E
end
end
end

return A
end

local function teleportBehind(A)
if A and A.Character and x.Character then
local B=A.Character:FindFirstChild"HumanoidRootPart"
local C=x.Character:FindFirstChild"HumanoidRootPart"

if B and C then
local D=B.Position
local E=B.CFrame.LookVector
local F=D-(E*u)
C.CFrame=CFrame.new(F,D)
end
end
end


local function toggleNoclip()
if s then
s:Disconnect()
s=nil
end

if r then
s=e.Stepped:Connect(function()
if x.Character then
for A,B in pairs(x.Character:GetDescendants())do
if B:IsA"BasePart"then
B.CanCollide=false
end
end
end
end)
end
end-- Aimbot Functions



local function getClosestPlayer()
local A
local B=math.huge

for C,D in pairs(c:GetPlayers())do
if D~=x then
local E=D.Character
if E and E:FindFirstChild"Head"and E:FindFirstChild"Humanoid"
and E.Humanoid.Health>0 then
local F=b:WorldToScreenPoint(E.Head.Position)
local G=Vector2.new(a:GetMouseLocation().X,a:GetMouseLocation().Y)
local H=(Vector2.new(F.X,F.Y)-G).Magnitude

if H<B and D.Team~=x.Team then
A=D
B=H
end
end
end
end

return A
end

local function aimAt(A)
if not A or not A.Character then return end

local B=A.Character:FindFirstChild"Head"
if not B then return end

local C=B.Position
local D=CFrame.new(b.CFrame.Position,C)

b.CFrame=b.CFrame:Lerp(D,v)
end-- Introduction Tab


local A=z:CreateTab"Introduction"
A:CreateSection"⭐ Welcome to Counter Blox Overhaul ⭐"
A:CreateButton{
Name="Copy Discord Invite",
Callback=function()
setclipboard"https://discord.gg/xX5SVJFJYA"
y:Notify{
Title="Discord Invite",
Content="Discord invite has been copied to your clipboard!",
Duration=3,
Image=4483362458,
Actions={
Ignore={
Name="Okay!",
Callback=function()
print"User acknowledged notification"
end
},
},
}
end
}-- Aim Tab


local B=z:CreateTab"Combat"
B:CreateSection"🎯 Targeting Features"

B:CreateToggle{
Name="Aimbot",
CurrentValue=false,
Flag="Aimbot",
Callback=function(C)
w.aimbotEnabled=C
end,
}

B:CreateSlider{
Name="Aim Smoothness",
Range={0.1,1},
Increment=0.1,
Suffix="",
CurrentValue=v,
Flag="AimSmoothness",
Callback=function(C)
v=C
end,
}-- ESP Tab


local C=z:CreateTab"Visuals"
C:CreateSection"👁️ Player ESP Settings"-- Main Tab



C:CreateToggle{
Name="Enable ESP",
CurrentValue=true,
Flag="MasterToggle",
Callback=function(D)
k.enabled=D
if not D then
cleanupAllESP()
end
end
}

C:CreateToggle{
Name="Boxes",
CurrentValue=false,
Callback=function(D)k.boxes=D end
}

C:CreateToggle{
Name="Names",
CurrentValue=false,
Callback=function(D)k.names=D end
}

C:CreateToggle{
Name="Tracers",
CurrentValue=false,
Callback=function(D)k.tracers=D end
}

C:CreateToggle{
Name="Health Bars",
CurrentValue=false,
Callback=function(D)k.health=D end
}

C:CreateToggle{
Name="Show Health Number",
CurrentValue=false,
Callback=function(D)k.showHealthNumber=D end
}



C:CreateToggle{
Name="Show Distance",
CurrentValue=false,
Callback=function(D)k.showDistance=D end
}

C:CreateToggle{
Name="Team Colors",
CurrentValue=false,
Callback=function(D)k.teamColor=D end
}

C:CreateSlider{
Name="Text Size",
Range={8,24},
Increment=1,
Suffix="px",
CurrentValue=8,
Callback=function(D)
k.settings.textSize=D
for E,F in pairs(c:GetPlayers())do
if k.nameElements[F]then
k.nameElements[F].Size=D
end
if k.healthElements[F]then
k.healthElements[F].text.Size=D
end
end
end
}

C:CreateSlider{
Name="Box Thickness",
Range={1,5},
Increment=1,
Suffix="px",
CurrentValue=1,
Callback=function(D)
k.settings.boxThickness=D
for E,F in pairs(c:GetPlayers())do
if k.boxElements[F]then
k.boxElements[F].Thickness=D
end
end
end
}

C:CreateSlider{
Name="Tracer Thickness",
Range={1,5},
Increment=1,
Suffix="px",
CurrentValue=1,
Callback=function(D)
k.settings.tracerThickness=D
for E,F in pairs(c:GetPlayers())do
if k.tracerElements[F]then
k.tracerElements[F].Thickness=D
end
end
end
}

C:CreateSlider{
Name="ESP Transparency",
Range={0,1},
Increment=0.1,
Suffix="%",
CurrentValue=1,
Callback=function(D)
k.settings.transparency=D
for E,F in pairs(c:GetPlayers())do
if k.boxElements[F]then
k.boxElements[F].Transparency=D
k.nameElements[F].Transparency=D
k.tracerElements[F].Transparency=D

local G=k.healthElements[F]
G.background.Transparency=D
G.fill.Transparency=D
G.outline.Transparency=D
G.text.Transparency=D
end
end
end
}

C:CreateSlider{
Name="Max Distance",
Range={100,5000},
Increment=100,
Suffix="m",
CurrentValue=1000,
Callback=function(D)
k.maxDistance=D
end
}

C:CreateColorPicker{
Name="ESP Color",
Color=k.colors.box,
Callback=function(D)
k.colors.box=D
k.colors.tracer=D
for E,F in pairs(c:GetPlayers())do
if not k.teamColor and k.boxElements[F]then
k.boxElements[F].Color=D
k.tracerElements[F].Color=D
end
end
end
}

C:CreateColorPicker{
Name="Text Color",
Color=k.colors.text,
Callback=function(D)
k.colors.text=D
for E,F in pairs(c:GetPlayers())do
if k.nameElements[F]then
k.nameElements[F].Color=D
end
end
end
}-- Chams Tab


local D=z:CreateTab"Chams"
D:CreateSection"💫 Player Highlights"

D:CreateToggle{
Name="Enable Chams",
CurrentValue=false,
Flag="ChamsEnabled",
Callback=function(E)
j.ChamsEnabled=E
UpdateAllPlayers()
end
}

D:CreateColorPicker{
Name="Chams Color",
Color=g,
Flag="ChamsColor",
Callback=function(E)
g=E
if not j.UseTeamColor then
for F,G in ipairs(c:GetPlayers())do
UpdateHighlight(G)
end
end
end
}

D:CreateSlider{
Name="Inner Visibility",
Range={0,1},
Increment=0.1,
Suffix="Transparency",
CurrentValue=1,
Flag="InnerTransparency",
Callback=function(E)
j.InnerTransparency=E
for F,G in ipairs(c:GetPlayers())do
UpdateHighlight(G)
end
end
}

D:CreateToggle{
Name="Don't Show Teammates",
CurrentValue=false,
Flag="TeamCheck",
Callback=function(E)
j.TeamCheck=E
UpdateAllPlayers()
end
}

D:CreateToggle{
Name="Use Team Colors",
CurrentValue=false,
Flag="TeamColors",
Callback=function(E)
j.UseTeamColor=E
UpdateAllPlayers()
end
}

D:CreateToggle{
Name="Show Names",
CurrentValue=false,
Flag="ShowNames",
Callback=function(E)
j.ShowNames=E
UpdateAllPlayers()
end
}



local E=z:CreateTab"Misc"
D:CreateSection"Additional Features"



E:CreateToggle{
Name="Noclip",
CurrentValue=false,
Flag="Noclip",
Callback=function(F)
r=F
toggleNoclip()
end
}

E:CreateLabel("Kill Aura is prefect for fighting other exploiters",4483362458,Color3.fromRGB(0,155,255),true)-- Title, Icon, Color, IgnoreTheme

E:CreateToggle{
Name="Kill Aura",
CurrentValue=false,
Flag="KillAuraToggle",
Callback=function(F)
t=F

end,
}


E:CreateSlider{
Name="Teleport Distance",
Range={1,10},
Increment=0.5,
Suffix="Studs",
CurrentValue=3,
Flag="TeleportDistance",
Callback=function(F)
u=F
end,
}-- Create Button


E:CreateButton{
Name="Reset Position",
Callback=function()
if x.Character then
x.Character:FindFirstChild"Humanoid".Health=0
end
end,
}-- Main Loop


e.Heartbeat:Connect(function()
if t then
local F=getClosestEnemy()
if F then
teleportBehind(F)

pcall(function()
local G={
[1]=F.Character.Head,
[2]=F.Character.Head.Position,
[3]="Scout",
[4]=100,
[5]=x.Character.Gun
}
game:GetService"ReplicatedStorage".Events.DamagePlayer:FireServer(unpack(G))
end)
end
end
end)-- Aimbot Connection





local F

a.InputBegan:Connect(function(G)
if G.UserInputType==Enum.UserInputType.MouseButton1 then
w.aim=true

if w.aimbotEnabled and not F then
F=e.RenderStepped:Connect(function()
if w.aim and w.aimbotEnabled then
local H=getClosestPlayer()
if H then
aimAt(H)
end
end
end)
end
end
end)

a.InputEnded:Connect(function(G)
if G.UserInputType==Enum.UserInputType.MouseButton1 then
w.aim=false
if F then
F:Disconnect()
F=nil
end
end
end)-- Clean up


game.Players.LocalPlayer.OnTeleport:Connect(function()
if F then
F:Disconnect()
end
end)

d.ChildRemoved:Connect(function(G)
if G.Name=="Rayfield"then
for H,I in pairs(h)do
I:Destroy()
end
for H,I in pairs(i)do
I:Destroy()
end
table.clear(h)
table.clear(i)
end
end)

game.Players.LocalPlayer.CharacterAdded:Connect(function()
if r then
toggleNoclip()
end
end)-- Connect update function


e.RenderStepped:Connect(updateESP)-- Cleanup on player removal


c.PlayerRemoving:Connect(cleanupESPElement)
