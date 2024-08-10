--[[ 
    @title
        fatality.universal (lua)
    
    @author
        sleeperweda

    @description
        Beware of ðŸ code.
]]

local SYC = {
    Modules = {
        UI = {}
    }
}

-- check if there is no global variable SYC, and if there is none, it will set it to the table above.
-- erm its basically prefetch ifykyk
if not getgenv().SYC then
    getgenv().SYC = SYC
end

if not getgenv().load_game then
    getgenv().load_game = "da_hood"
end

if not isfolder("drax") then
    makefolder("drax")
end

if not isfolder("drax/configs") then
    makefolder("drax/configs")
end

if not isfolder("drax/configs/da_hood") then
    makefolder("drax/configs/da_hood")
end

-- make services global. self-explanatory.
getgenv().playerService = game:GetService("Players")
getgenv().coreguiService = game:GetService("CoreGui")
getgenv().tweenService = game:GetService("TweenService")
getgenv().inputService = game:GetService("UserInputService")
getgenv().rsService = game:GetService("RunService")
getgenv().replicatedStorage = game:GetService("ReplicatedStorage")
getgenv().textService = game:GetService("TextService")
getgenv().httpService = game:GetService("HttpService")
getgenv().userSettings = UserSettings()
getgenv().userGameSettings = userSettings:GetService("UserGameSettings")
getgenv().inputManager = game:GetService("VirtualInputManager")

local LocalPlayer = playerService.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- localization of lua libraries. this reduces the need to repeatedly look up these global libraries.
-- what the actual fuck weda.
local MathHuge, MathAbs, MathAcos, MathAsin, MathAtan, MathAtan2, MathCeil, MathCos, MathCosh, MathDeg, MathExp, MathFloor, MathFmod, MathFrexp, MathLdexp, MathLog, MathLog10, MathMax, MathMin, MathModf, MathPi, MathPow, MathRad, MathRandom, MathRandomseed, MathSin, MathSinh, MathSqrt, MathTan, MathTanh = math.huge, math.abs, math.acos, math.asin, math.atan, math.atan2, math.ceil, math.cos, math.cosh, math.deg, math.exp, math.floor, math.fmod, math.frexp, math.ldexp, math.log, math.log10, math.max, math.min, math.modf, math.pi, math.pow, math.rad, math.random, math.randomseed, math.sin, math.sinh, math.sqrt, math.tan, math.tanh
local TableConcat, TableInsert, TablePack, TableRemove, TableSort, TableUnpack, TableClear, TableFind = table.concat, table.insert, table.pack, table.remove, table.sort, table.unpack, table.clear, table.find
local Vector2New, Vector2Zero, Vector2New = Vector2.new, Vector2.zero, Vector2.new
local Vector3New, Vector3Zero, Vector3One, Vector3FromNormalId, Vector3FromAxis = Vector3.new, Vector3.zero, Vector3.one, Vector3.FromNormalId, Vector3.FromAxis
local UDim2New = UDim2.new
local CFrameNew, CFrameAngles, CFrameFromAxisAngle, CFrameFromEulerAnglesXYZ, CFrameFromMatrix, CFrameFromOrientation, CFrameFromQuaternion = CFrame.new, CFrame.Angles, CFrame.fromAxisAngle, CFrame.fromEulerAnglesXYZ, CFrame.fromMatrix, CFrame.fromOrientation, CFrame.fromQuaternion
local Color3New, Color3FromRGB, Color3FromHSV = Color3.new, Color3.fromRGB, Color3.fromHSV
local InstanceNew = Instance.new
local TaskDelay, TaskSpawn, TaskWait = task.delay, task.spawn, task.wait
local RaycastParamsNew = RaycastParams.new
local DrawingNew = Drawing.new

-- importing of files, this is bundled with a bundler.
local ModuleHandler = (function() -- src/Lua/Modules/ModuleHandler.lua
    -- similar to lua require, but it is for a certain table. Such as SYC.Modules
    
    local ModuleHandler = {}
    
    function ModuleHandler:include(ModuleName)
        if not SYC then return end
        if not SYC.Modules then return end
    
        if not type(ModuleName) == "string" then return end
    
        local Modules = SYC.Modules
        return Modules[ModuleName]
    end
    
    getgenv().include = function (modname) return ModuleHandler:include(modname) end 
    return ModuleHandler
end)()

do -- src/Lua/Modules/Base/
    do -- src/Lua/Modules/Base/Connection.lua
        function SYC.Modules.Connect(onething, secondthing)
            local connection = onething:Connect(secondthing)
            return connection
        end
    end
    do -- src/Lua/Modules/Base/Draw.lua
        local DrawingClass = {}
        DrawingClass.__index = DrawingClass
        DrawingClass.Objects = {}
        
        local DrawingMeta = {}
        
        DrawingMeta.__call = function (self, Arguments)
            if Arguments then
                local newObject = Drawing.new(Arguments[1])
                
                for property, value in next, Arguments[2] do
                    newObject[property] = value
                end
        
                table.insert(self.Objects, newObject)
                return newObject
            end
        end
        
        setmetatable(DrawingClass, DrawingMeta)
        
        SYC.Modules.DrawingClass = DrawingClass
    end
    do -- src/Lua/Modules/Base/Lerp.lua
        function SYC.Modules.lerp(a, b, t)
            return a + (b - a) * t
        end
    end
    do -- src/Lua/Modules/Base/Loops.lua
        local Loops = {Heartbeat = {}, RenderStepped = {}}
        function Loops:AddToHeartbeat(Name, Function)
            if Loops["Heartbeat"][Name] == nil then
                Loops["Heartbeat"][Name] = rsService.Heartbeat:Connect(Function)
            end
        end
        function Loops:RemoveFromHeartbeat(Name)
            if Loops["Heartbeat"][Name] then
                Loops["Heartbeat"][Name]:Disconnect()
                Loops["Heartbeat"][Name] = nil
            end
        end
        function Loops:AddToRenderStepped(Name, Function)
            if Loops["RenderStepped"][Name] == nil then
                Loops["RenderStepped"][Name] = rsService.RenderStepped:Connect(Function)
            end
        end
        function Loops:RemoveFromRenderStepped(Name)
            if Loops["RenderStepped"][Name] then
                Loops["RenderStepped"][Name]:Disconnect()
                Loops["RenderStepped"][Name] = nil
            end
        end
        
        SYC.Modules.Loops = Loops
    end
    do -- src/Lua/Modules/Base/PerlinNoise.lua
        -- useful shit for legit ig
        function SYC.Modules.PerlinNoise(offset, speed, time)
            local value = math.noise(time * speed + offset)
            return math.clamp(value, -0.5, 0.5)
        end
    end
    do -- src/Lua/Modules/Base/UI - Rich to Plain.lua
        -- @https://devforum.roblox.com/t/how-to-ensure-a-plain-text-string-when-using-rich-text-field/1640202
        function SYC.Modules.UI.RichTextToNormalText(str)
            local output_string = str
            while true do 
                if not output_string:find("<") and not output_string:find(">") then break end -- If not found  any <...>
                if (output_string:find("<") and not output_string:find(">")) or (output_string:find(">") and not output_string:find("<")) then return error("Invalid RichText") end -- if found only "<..." or "...>"
                output_string = output_string:gsub(output_string:sub(output_string:find("<"),output_string:find(">")),"",1) -- Removing this "<...>"
                TaskWait()
            end
            return output_string
        end
    end
    do -- src/Lua/Modules/Base/UI -GetTextBoundary.lua
        function SYC.Modules.UI:GetTextBoundary(Text, Font, Size, Resolution)
            local Bounds = textService:GetTextSize(Text, Size, Font, Resolution or Vector2New(1920, 1080))
            return Bounds.X, Bounds.Y
        end
    end
end

local UserInterface = (function() -- src/Lua/Interface/Interface.Lua
    local UserInterface = {
        Instances = {},
        Popup = nil,
        KeybindsListObjects = {},
        KeybindList = nil,
    
        Flags = {},
        ConfigFlags = {}
    }
    getgenv().uishit = UserInterface
    
    getgenv().theme = {
        accent = Color3FromRGB(168, 157, 159)
    }
    
    getgenv().theme_event = Instance.new('BindableEvent')
    
    getgenv().UI = UserInterface.Instances
    local UIModule = include "UI"
    
    local dragging, dragInput, dragStart, startPos, dragObject
    
    local Keys = {
        [Enum.KeyCode.LeftShift] = "LS",
        [Enum.KeyCode.RightShift] = "RS",
        [Enum.KeyCode.LeftControl] = "LC",
        [Enum.KeyCode.RightControl] = "RC",
        [Enum.KeyCode.LeftAlt] = "LA",
        [Enum.KeyCode.RightAlt] = "RA",
        [Enum.KeyCode.CapsLock] = "CAPS",
        [Enum.KeyCode.Return] = "ENT",
        [Enum.KeyCode.PageDown] = "PGD",
        [Enum.KeyCode.PageUp] = "PGU",
        [Enum.KeyCode.ScrollLock] = "SCL",
        [Enum.KeyCode.One] = "1",
        [Enum.KeyCode.Two] = "2",
        [Enum.KeyCode.Three] = "3",
        [Enum.KeyCode.Four] = "4",
        [Enum.KeyCode.Five] = "5",
        [Enum.KeyCode.Six] = "6",
        [Enum.KeyCode.Seven] = "7",
        [Enum.KeyCode.Eight] = "8",
        [Enum.KeyCode.Nine] = "9",
        [Enum.KeyCode.Zero] = "0",
        [Enum.KeyCode.KeypadOne] = "1",
        [Enum.KeyCode.KeypadTwo] = "2",
        [Enum.KeyCode.KeypadThree] = "3",
        [Enum.KeyCode.KeypadFour] = "4",
        [Enum.KeyCode.KeypadFive] = "5",
        [Enum.KeyCode.KeypadSix] = "6",
        [Enum.KeyCode.KeypadSeven] = "7",
        [Enum.KeyCode.KeypadEight] = "8",
        [Enum.KeyCode.KeypadNine] = "9",
        [Enum.KeyCode.KeypadZero] = "0",
        [Enum.KeyCode.Minus] = "-",
        [Enum.KeyCode.Equals] = "=",
        [Enum.KeyCode.Tilde] = "~",
        [Enum.KeyCode.LeftBracket] = "[",
        [Enum.KeyCode.RightBracket] = "]",
        [Enum.KeyCode.RightParenthesis] = ")",
        [Enum.KeyCode.LeftParenthesis] = "(",
        [Enum.KeyCode.Semicolon] = ",",
        [Enum.KeyCode.Quote] = "'",
        [Enum.KeyCode.BackSlash] = "\\",
        [Enum.KeyCode.Comma] = ",",
        [Enum.KeyCode.Period] = ".",
        [Enum.KeyCode.Slash] = "/",
        [Enum.KeyCode.Asterisk] = "*",
        [Enum.KeyCode.Plus] = "+",
        [Enum.KeyCode.Period] = ".",
        [Enum.KeyCode.Backquote] = "`",
        [Enum.KeyCode.Insert] = "INS",
        [Enum.UserInputType.MouseButton1] = "MB1",
        [Enum.UserInputType.MouseButton2] = "MB2",
        [Enum.UserInputType.MouseButton3] = "MB3",
        [Enum.KeyCode.Backspace] = "BS",
        [Enum.KeyCode.Escape] = "ESC",
        [Enum.KeyCode.Space] = "SPC",
    }
    
    local FlagCount = 0
    function UserInterface:GetNextFlag()
        FlagCount = FlagCount + 1
        return tostring(FlagCount)
    end
    
    function UserInterface:Create(OptionsLaughtOutLouds)
        local Configuration = {
            Tabs = {},
            Title = OptionsLaughtOutLouds.title or 'syndicate<font color="rgb(129, 127, 127)">.club</font>'
        }
    
        local Texts = {
            "user",
        }
    
        local function ChangeText(Object, NewText) -- this is for the thing in the top-right in the ui what
            tweenService:Create(Object, TweenInfo.new(0.2), {TextTransparency = 0.5}):Play()
            Object.Text = NewText
            TaskWait(0.1)
    
            tweenService:Create(Object, TweenInfo.new(0.2), {TextTransparency = 0}):Play()
        end
        
        UI["1"] = InstanceNew("ScreenGui", coreguiService)
        UI["1"]["Name"] = [[syndicate.club]]
        UI["1"]["ZIndexBehavior"] = Enum.ZIndexBehavior.Global
    
        UI["2"] = InstanceNew("Frame", UI["1"])
        UI["2"]["BorderSizePixel"] = 0
        UI["2"]["BackgroundColor3"] = Color3FromRGB(24, 24, 24)
        UI["2"]["Size"] = UDim2New(0, 562, 0, 459)
        UI["2"]["Position"] = UDim2New(0, 527, 0, 168)
        UI["2"]["BorderColor3"] = Color3FromRGB(0, 0, 0)
        UI["2"]["Name"] = [[BackgroundFrame]]
        
        UI["3"] = InstanceNew("UICorner", UI["2"])
        UI["3"]["Name"] = [[BackgroundCorner]]
        
        UI["4"] = InstanceNew("UIStroke", UI["2"])
        UI["4"]["ApplyStrokeMode"] = Enum.ApplyStrokeMode.Border
        UI["4"]["Name"] = [[BackgroundStroke]]
        UI["4"]["Thickness"] = 2
        UI["4"]["Color"] = Color3FromRGB(31, 33, 31)
        
        UI["5"] = InstanceNew("TextLabel", UI["2"])
        UI["5"]["TextStrokeTransparency"] = 0
        UI["5"]["BorderSizePixel"] = 0
        UI["5"]["BackgroundColor3"] = Color3FromRGB(255, 255, 255)
        UI["5"]["TextSize"] = 16
        UI["5"]["FontFace"] = Font.new([[rbxasset://fonts/families/SourceSansPro.json]], Enum.FontWeight.Regular, Enum.FontStyle.Normal)
        UI["5"]["TextColor3"] = Color3FromRGB(255, 255, 255)
        UI["5"]["BackgroundTransparency"] = 1
        UI["5"]["Size"] = UDim2New(0, 81, 0, 20)
        UI["5"]["BorderColor3"] = Color3FromRGB(0, 0, 0)
        UI["5"]["Text"] = Configuration.Title
        UI["5"]["Name"] = [[MainTitle]]
        UI["5"]["Position"] = UDim2New(0, 15, 0, 12)
        UI["5"]["RichText"] = true
        UI["5"]["TextXAlignment"] = Enum.TextXAlignment.Left
    
        UI["6"] = InstanceNew("Frame", UI["2"])
        UI["6"]["BackgroundColor3"] = Color3FromRGB(255, 255, 255)
        UI["6"]["Size"] = UDim2New(0, 1, 0, 16)
        UI["6"]["Position"] = UDim2New(0, 98, 0, 14)
        UI["6"]["BorderColor3"] = Color3FromRGB(0, 0, 0)
        UI["6"]["Name"] = [[BackgroundAccent]]
    
        UI["7"] = InstanceNew("Frame", UI["2"])
        UI["7"]["BorderSizePixel"] = 0
        UI["7"]["BackgroundColor3"] = Color3FromRGB(255, 255, 255)
        UI["7"]["Size"] = UDim2New(0, 456, 0, 16)
        UI["7"]["Position"] = UDim2New(0, 105, 0, 14)
        UI["7"]["BorderColor3"] = Color3FromRGB(0, 0, 0)
        UI["7"]["Name"] = [[TabsList]]
        UI["7"]["BackgroundTransparency"] = 1
    
        UI["9"] = InstanceNew("UIListLayout", UI["7"])
        UI["9"]["Padding"] = UDim.new(0, 5)
        UI["9"]["SortOrder"] = Enum.SortOrder.LayoutOrder
        UI["9"]["Name"] = [[TabsListLayout]]
        UI["9"]["FillDirection"] = Enum.FillDirection.Horizontal
    
        UI["a"] = InstanceNew("TextLabel", UI["2"])
        UI["a"]["TextWrapped"] = false
        UI["a"]["TextStrokeTransparency"] = 0
        UI["a"]["BorderSizePixel"] = 0
        UI["a"]["TextXAlignment"] = Enum.TextXAlignment.Right
        UI["a"]["TextScaled"] = false
        UI["a"]["BackgroundColor3"] = Color3FromRGB(255, 255, 255)
        UI["a"]["TextSize"] = 16
        UI["a"]["FontFace"] = Font.new([[rbxasset://fonts/families/SourceSansPro.json]], Enum.FontWeight.Regular, Enum.FontStyle.Normal)
        UI["a"]["TextColor3"] = Color3FromRGB(255, 255, 255)
        UI["a"]["BackgroundTransparency"] = 1
        UI["a"]["Size"] = UDim2New(0, 452, 0, 19)
        UI["a"]["BorderColor3"] = Color3FromRGB(0, 0, 0)
        UI["a"]["Text"] = [[powered by astro.space]]
        UI["a"]["Name"] = [[CreditTitle]]
        UI["a"]["Position"] = UDim2New(0, 96, 0, 428)
    
        UI["b"] = InstanceNew("Frame", UI["2"])
        UI["b"]["BorderSizePixel"] = 0
        UI["b"]["BackgroundColor3"] = Color3FromRGB(17, 17, 17)
        UI["b"]["Size"] = UDim2New(0, 533, 0, 378)
        UI["b"]["Position"] = UDim2New(0.027, 0,0.095, 0)
        UI["b"]["BorderColor3"] = Color3FromRGB(0, 0, 0)
        UI["b"]["Name"] = [[MainFrame]]
    
        local MainFrameShadow1 = Instance.new("Frame")
        local MF_SHADOW1 = Instance.new("UIGradient")
        
        MainFrameShadow1.Name = "MainFrameShadow1"
        MainFrameShadow1.Parent = UI["b"]
        MainFrameShadow1.ZIndex = 2
        MainFrameShadow1.Size = UDim2.new(1, 0, 0.039682541, 0)
        MainFrameShadow1.BorderColor3 = Color3.fromRGB(0, 0, 0)
        MainFrameShadow1.Position = UDim2.new(0, 0, 0.960317433, 0)
        MainFrameShadow1.BorderSizePixel = 0
        MainFrameShadow1.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        
        MF_SHADOW1.Name = "MF_SHADOW1"
        MF_SHADOW1.Parent = MainFrameShadow1
        MF_SHADOW1.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0), NumberSequenceKeypoint.new(0.004552352242171764, 0.11475414037704468), NumberSequenceKeypoint.new(0.030349012464284897, 0.3606557846069336), NumberSequenceKeypoint.new(0.6358118057250977, 1), NumberSequenceKeypoint.new(0.9998999834060669, 1), NumberSequenceKeypoint.new(1, 0)})
        MF_SHADOW1.Rotation = -90
    
        local MainFrameShadow2 = Instance.new("Frame")
        local MF_SHADOW2 = Instance.new("UIGradient")
        
        MainFrameShadow2.Name = "MainFrameShadow2"
        MainFrameShadow2.Parent = UI["b"]
        MainFrameShadow2.ZIndex = 2
        MainFrameShadow2.Size = UDim2.new(1, 0, 0.0399999991, 0)
        MainFrameShadow2.BorderColor3 = Color3.fromRGB(0, 0, 0)
        MainFrameShadow2.BorderSizePixel = 0
        MainFrameShadow2.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        
        MF_SHADOW2.Name = "MF_SHADOW2"
        MF_SHADOW2.Parent = MainFrameShadow2
        MF_SHADOW2.Transparency = NumberSequence.new({NumberSequenceKeypoint.new(0, 0), NumberSequenceKeypoint.new(0.004552352242171764, 0.11475414037704468), NumberSequenceKeypoint.new(0.030349012464284897, 0.3606557846069336), NumberSequenceKeypoint.new(0.6358118057250977, 1), NumberSequenceKeypoint.new(0.9998999834060669, 1), NumberSequenceKeypoint.new(1, 0)})
        MF_SHADOW2.Rotation = 90
    
        UI["c"] = InstanceNew("UICorner", UI["b"])
        UI["c"]["Name"] = [[MainFrameCorner]]
    
        UI["d"] = InstanceNew("UIStroke", UI["b"])
        UI["d"]["ApplyStrokeMode"] = Enum.ApplyStrokeMode.Border
        UI["d"]["Name"] = [[MainFrameStroke]]
        UI["d"]["Color"] = Color3FromRGB(29, 29, 29)
    
        UI["e"] = InstanceNew("Folder", UI["2"])
        UI["e"]["Name"] = [[Sections]]
    
        local Shadow1 = Instance.new("ImageLabel")
    
        Shadow1.Name = "Shadow1"
        Shadow1.Parent = UI["2"]
        Shadow1.AnchorPoint = Vector2.new(0.5, 0.5)
        Shadow1.ZIndex = 0
        Shadow1.Size = UDim2.new(1.7, 0,2.843, 0)
        Shadow1.BorderColor3 = Color3.fromRGB(0, 0, 0)
        Shadow1.Rotation = 90
        Shadow1.BackgroundTransparency = 1
        Shadow1.Position = UDim2.new(0.468, 0,0.495, 0)
        Shadow1.BorderSizePixel = 0
        Shadow1.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        Shadow1.ImageColor3 = Color3.fromRGB(0, 0, 0)
        Shadow1.ScaleType = Enum.ScaleType.Tile
        Shadow1.Image = "rbxassetid://8992230677"
        Shadow1.SliceCenter = Rect.new(Vector2.new(0, 0), Vector2.new(99, 99))
    
        local text_coroutine = coroutine.create(function ()
            while TaskWait() do
                for i = 1, #Texts do
                    TaskWait(2)
                    ChangeText(UI["a"], Texts[i])
                end
            end
        end)
        coroutine.resume(text_coroutine)
    
        function Configuration:Tab( Tab_Name )
            if not type(Tab_Name) == "string" then return end
    
            local TabConfiguration = { Sections = {} }
    
            local X = UIModule:GetTextBoundary(Tab_Name, Enum.Font.SourceSans, 16)
            UI["8"] = InstanceNew("TextButton", UI["7"])
            UI["8"]["TextStrokeTransparency"] = 0
            UI["8"]["BorderSizePixel"] = 0
            UI["8"]["TextSize"] = 16
            UI["8"]["TextColor3"] = Color3FromRGB(137, 137, 139)
            UI["8"]["BackgroundColor3"] = Color3FromRGB(255, 255, 255)
            UI["8"]["FontFace"] = Font.new([[rbxasset://fonts/families/SourceSansPro.json]], Enum.FontWeight.Regular, Enum.FontStyle.Normal)
            UI["8"]["Size"] = UDim2New(0, X, 1, 0)
            UI["8"]["BackgroundTransparency"] = 1
            UI["8"]["Name"] = [[TabButton]]
            UI["8"]["BorderColor3"] = Color3FromRGB(0, 0, 0)
            UI["8"]["Text"] = Tab_Name
    
            UI["f"] = InstanceNew("Frame", UI["e"])
            UI["f"]["Active"] = true
            UI["f"]["BorderSizePixel"] = 0
            UI["f"]["BackgroundColor3"] = Color3FromRGB(255, 255, 255)
            UI["f"]["Name"] = [[MainSectionFrame]]
            UI["f"]["Position"] = UDim2New(0.028, 0,0.142, 0)
            UI["f"]["Size"] = UDim2New(0, 530, 0, 378)
  
