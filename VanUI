-- Vanith UI Library

local Players          = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local HttpService      = game:GetService("HttpService")

local Vanith = {}

local Window  = {}
Window.__index = Window

local Tab     = {}
Tab.__index   = Tab

local Section = {}
Section.__index = Section

-- helpers ------------------------------------------------------------

local function New(className, props)
    local inst = Instance.new(className)

    -- damit nix auto-lokalisiert wird
    pcall(function()
        inst.AutoLocalize = false
    end)

    if props then
        for k, v in pairs(props) do
            inst[k] = v
        end
    end
    return inst
end

local function IsMouse(t)
    return t == Enum.UserInputType.MouseButton1
        or t == Enum.UserInputType.MouseButton2
        or t == Enum.UserInputType.MouseButton3
end

local function IsTouch(t)
    return t == Enum.UserInputType.Touch
end

local function Clamp01(x)
    return math.clamp(x, 0, 1)
end

local function RoundStep(v, minv, step)
    if not step or step <= 0 then return v end
    local n = (v - minv) / step
    return (math.floor(n + 0.5) * step) + minv
end

-- nur für Slider-Texte und Fenster-Titel
local function AutoFitText(label, maxSize, minSize, padding)
    if not label or not (label:IsA("TextLabel") or label:IsA("TextButton")) then
        return
    end

    maxSize = maxSize or label.TextSize or 12
    minSize = minSize or 8
    padding = padding or 4

    local function update()
        local w = label.AbsoluteSize.X
        if w <= 0 then return end

        for size = maxSize, minSize, -1 do
            label.TextSize = size
            if label.TextBounds.X <= (w - padding) then
                break
            end
        end
    end

    label:GetPropertyChangedSignal("Text"):Connect(update)
    label:GetPropertyChangedSignal("AbsoluteSize"):Connect(update)
    task.defer(update)
end

local function PlayTween(inst, props, time)
    if not inst then return end
    time = time or 0.15
    local ok, tween = pcall(
        TweenService.Create,
        TweenService,
        inst,
        TweenInfo.new(time, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
        props
    )
    if ok and tween then tween:Play() end
end

local function GetRootParent()
    local ok, cg = pcall(function()
        if gethui then
            return gethui()
        end
        return game:GetService("CoreGui")
    end)
    if ok and cg then
        return cg
    end

    local lp = Players.LocalPlayer
    return lp and lp:FindFirstChildOfClass("PlayerGui") or nil
end

-- theme --------------------------------------------------------------

local DEFAULT_THEME = {
    Accent  = Color3.fromRGB(165, 105, 255),

    BG0     = Color3.fromRGB(4, 4, 6),
    BG1     = Color3.fromRGB(10, 10, 14),
    BG2     = Color3.fromRGB(10, 10, 14),

    Stroke  = Color3.fromRGB(40, 40, 50),

    Text    = Color3.fromRGB(230, 230, 238),
    Muted   = Color3.fromRGB(140, 140, 150),
    DimText = Color3.fromRGB(95, 95, 110),

    Track   = Color3.fromRGB(22, 22, 28),
    Field   = Color3.fromRGB(16, 16, 20),
}

-- externe Theme-Registry (fÃ¼r eingebettete Themes)
Vanith.RegisteredThemes = {}

function Vanith:RegisterTheme(name, def)
    if type(name) ~= "string" or type(def) ~= "table" then
        return
    end
    self.RegisteredThemes[name] = def
end

function Vanith:GetTheme(name)
    return self.RegisteredThemes[name]
end

function Vanith:GetThemes()
    local copy = {}
    for k, v in pairs(self.RegisteredThemes) do
        copy[k] = v
    end
    return copy
end

-- Vanith UI - externe Theme-/Hintergrund-Definitionen
-- (lokal eingebettet, keine externe Datei)
do
    local VanithRef = Vanith

    local Players      = game:GetService("Players")
    local TweenService = game:GetService("TweenService")

    local Themes = {}

    -- einfacher Helper fÃ¼r sicheren Holder-Zugriff
    local function getHolder(win)
        if win.BackgroundHolder and win.BackgroundHolder.Parent then
            return win.BackgroundHolder
        end
        if win.Main and win.Main.Parent then
            return win.Main
        end
        return nil
    end

    -- Winter-Theme mit simplen "Schneeflocken" -------------------------------

    local function makeWinterBackground(win)
        local holder = getHolder(win)
        if not holder then return end

        local folder = Instance.new("Folder")
        folder.Name = "Vanith_WinterBackground"
        folder.Parent = holder

        -- leicht blaue TÃ¶nung im Hintergrund
        local tint = Instance.new("Frame")
        tint.Name = "Tint"
        tint.BorderSizePixel = 0
        tint.BackgroundColor3 = Color3.fromRGB(10, 14, 32)
        tint.BackgroundTransparency = 0.6
        tint.Size = UDim2.fromScale(1, 1)
        tint.ZIndex = 0
        tint.Parent = folder

        -- einfache Schneeflocken als kleine weiÃŸe Punkte mit Tween nach unten
        local flakes = {}
        local flakeCount = 40
        for i = 1, flakeCount do
            local flake = Instance.new("Frame")
            flake.BorderSizePixel = 0
            flake.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            flake.BackgroundTransparency = 0.1
            flake.Size = UDim2.new(0, 2, 0, 2)
            flake.Position = UDim2.new(math.random(), 0, math.random(), 0)
            flake.ZIndex = 1
            flake.Parent = folder

            local duration = 5 + math.random() * 5
            local targetY = 1.1

            local tween = TweenService:Create(
                flake,
                TweenInfo.new(duration, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, -1, false),
                {Position = UDim2.new(flake.Position.X.Scale, 0, targetY, 0)}
            )
            tween:Play()
            table.insert(flakes, {frame = flake, tween = tween})
        end

        -- Cleanup-Funktion zurÃ¼ckgeben
        return function()
            for _, item in ipairs(flakes) do
                if item.tween then
                    pcall(function() item.tween:Cancel() end)
                end
            end
            if folder.Parent then
                folder:Destroy()
            end
        end
    end

    Themes["Winter"] = {
        Theme = {
            Accent  = Color3.fromRGB(140, 190, 255),
            BG0     = Color3.fromRGB(8, 10, 20),
            BG1     = Color3.fromRGB(14, 18, 32),
            BG2     = Color3.fromRGB(18, 24, 40),
            Stroke  = Color3.fromRGB(70, 100, 150),
            Text    = Color3.fromRGB(230, 240, 255),
            Muted   = Color3.fromRGB(150, 170, 200),
            DimText = Color3.fromRGB(110, 130, 170),
            Track   = Color3.fromRGB(24, 28, 40),
            Field   = Color3.fromRGB(18, 20, 30),
        },
        Background = makeWinterBackground,
    }

    -- Einfaches Oster-Theme mit Pastellfarben --------------------------------

    local function makeEasterBackground(win)
        local holder = getHolder(win)
        if not holder then return end

        local folder = Instance.new("Folder")
        folder.Name = "Vanith_EasterBackground"
        folder.Parent = holder

        local base = Instance.new("Frame")
        base.Name = "Base"
        base.BorderSizePixel = 0
        base.BackgroundColor3 = Color3.fromRGB(255, 245, 250)
        base.BackgroundTransparency = 0.4
        base.Size = UDim2.fromScale(1, 1)
        base.ZIndex = 0
        base.Parent = folder

        local colors = {
            Color3.fromRGB(255, 204, 204),
            Color3.fromRGB(255, 229, 204),
            Color3.fromRGB(221, 255, 204),
            Color3.fromRGB(204, 238, 255),
            Color3.fromRGB(229, 204, 255),
        }

        for i = 1, 10 do
            local bubble = Instance.new("Frame")
            bubble.BorderSizePixel = 0
            bubble.BackgroundColor3 = colors[((i - 1) % #colors) + 1]
            bubble.BackgroundTransparency = 0.3
            bubble.Size = UDim2.new(0, 60, 0, 60)
            bubble.Position = UDim2.new(math.random(), -30, math.random(), -30)
            bubble.ZIndex = 1
            bubble.Parent = folder

            local corner = Instance.new("UICorner")
            corner.CornerRadius = UDim.new(1, 0)
            corner.Parent = bubble
        end

        return function()
            if folder.Parent then
                folder:Destroy()
            end
        end
    end

    Themes["Easter"] = {
        Theme = {
            Accent  = Color3.fromRGB(255, 170, 220),
            BG0     = Color3.fromRGB(255, 248, 252),
            BG1     = Color3.fromRGB(250, 236, 248),
            BG2     = Color3.fromRGB(244, 224, 244),
            Stroke  = Color3.fromRGB(220, 190, 220),
            Text    = Color3.fromRGB(90, 60, 90),
            Muted   = Color3.fromRGB(140, 110, 140),
            DimText = Color3.fromRGB(170, 140, 170),
            Track   = Color3.fromRGB(240, 220, 240),
            Field   = Color3.fromRGB(252, 244, 252),
        },
        Background = makeEasterBackground,
    }

    -- Neon-Theme mit bewegten Linien -----------------------------------------

    local function makeNeonBackground(win)
        local holder = getHolder(win)
        if not holder then return end

        local folder = Instance.new("Folder")
        folder.Name = "Vanith_NeonBackground"
        folder.Parent = holder

        local tint = Instance.new("Frame")
        tint.Name = "Tint"
        tint.BorderSizePixel = 0
        tint.BackgroundColor3 = Color3.fromRGB(8, 4, 16)
        tint.BackgroundTransparency = 0.15
        tint.Size = UDim2.fromScale(1, 1)
        tint.ZIndex = 0
        tint.Parent = folder

        local lines = {}
        for i = 1, 8 do
            local line = Instance.new("Frame")
            line.BorderSizePixel = 0
            line.BackgroundColor3 = Color3.fromRGB(0, 255, 200)
            line.BackgroundTransparency = 0.4
            line.Size = UDim2.new(1, 0, 0, 2)
            line.Position = UDim2.new(0, 0, (i - 1) / 8, 0)
            line.ZIndex = 1
            line.Parent = folder

            local duration = 3 + math.random() * 3
            local tween = TweenService:Create(
                line,
                TweenInfo.new(duration, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
                {Position = UDim2.new(0, 0, line.Position.Y.Scale + 0.08, 0)}
            )
            tween:Play()
            table.insert(lines, {frame = line, tween = tween})
        end

        return function()
            for _, item in ipairs(lines) do
                if item.tween then
                    pcall(function() item.tween:Cancel() end)
                end
            end
            if folder.Parent then
                folder:Destroy()
            end
        end
    end

    Themes["Neon"] = {
        Theme = {
            Accent  = Color3.fromRGB(0, 255, 200),
            BG0     = Color3.fromRGB(8, 6, 14),
            BG1     = Color3.fromRGB(14, 10, 24),
            BG2     = Color3.fromRGB(18, 14, 32),
            Stroke  = Color3.fromRGB(60, 40, 90),
            Text    = Color3.fromRGB(235, 235, 255),
            Muted   = Color3.fromRGB(150, 150, 180),
            DimText = Color3.fromRGB(120, 120, 150),
            Track   = Color3.fromRGB(24, 20, 36),
            Field   = Color3.fromRGB(16, 12, 24),
        },
        Background = makeNeonBackground,
    }

    -- Forest-Theme mit sanften "Lichtflecken" --------------------------------

    local function makeForestBackground(win)
        local holder = getHolder(win)
        if not holder then return end

        local folder = Instance.new("Folder")
        folder.Name = "Vanith_ForestBackground"
        folder.Parent = holder

        local base = Instance.new("Frame")
        base.Name = "Base"
        base.BorderSizePixel = 0
        base.BackgroundColor3 = Color3.fromRGB(14, 20, 14)
        base.BackgroundTransparency = 0.2
        base.Size = UDim2.fromScale(1, 1)
        base.ZIndex = 0
        base.Parent = folder

        local spots = {}
        for i = 1, 12 do
            local spot = Instance.new("Frame")
            spot.BorderSizePixel = 0
            spot.BackgroundColor3 = Color3.fromRGB(80, 120, 80)
            spot.BackgroundTransparency = 0.6
            spot.Size = UDim2.new(0, 120, 0, 120)
            spot.Position = UDim2.new(math.random(), -60, math.random(), -60)
            spot.ZIndex = 1
            spot.Parent = folder

            local corner = Instance.new("UICorner")
            corner.CornerRadius = UDim.new(1, 0)
            corner.Parent = spot

            local tween = TweenService:Create(
                spot,
                TweenInfo.new(6 + math.random() * 4, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
                {BackgroundTransparency = 0.3}
            )
            tween:Play()
            table.insert(spots, {frame = spot, tween = tween})
        end

        return function()
            for _, item in ipairs(spots) do
                if item.tween then
                    pcall(function() item.tween:Cancel() end)
                end
            end
            if folder.Parent then
                folder:Destroy()
            end
        end
    end

    Themes["Forest"] = {
        Theme = {
            Accent  = Color3.fromRGB(120, 190, 120),
            BG0     = Color3.fromRGB(10, 16, 10),
            BG1     = Color3.fromRGB(16, 22, 16),
            BG2     = Color3.fromRGB(20, 28, 20),
            Stroke  = Color3.fromRGB(60, 80, 60),
            Text    = Color3.fromRGB(230, 245, 230),
            Muted   = Color3.fromRGB(150, 170, 150),
            DimText = Color3.fromRGB(110, 130, 110),
            Track   = Color3.fromRGB(22, 28, 22),
            Field   = Color3.fromRGB(16, 20, 16),
        },
        Background = makeForestBackground,
    }

    -- Sunset-Theme mit warmem Verlauf und Partikeln ---------------------------

    local function makeSunsetBackground(win)
        local holder = getHolder(win)
        if not holder then return end

        local folder = Instance.new("Folder")
        folder.Name = "Vanith_SunsetBackground"
        folder.Parent = holder

        local base = Instance.new("Frame")
        base.Name = "Base"
        base.BorderSizePixel = 0
        base.BackgroundColor3 = Color3.fromRGB(40, 16, 10)
        base.BackgroundTransparency = 0.15
        base.Size = UDim2.fromScale(1, 1)
        base.ZIndex = 0
        base.Parent = folder

        local particles = {}
        for i = 1, 20 do
            local p = Instance.new("Frame")
            p.BorderSizePixel = 0
            p.BackgroundColor3 = Color3.fromRGB(255, 180, 120)
            p.BackgroundTransparency = 0.4
            p.Size = UDim2.new(0, 3, 0, 3)
            p.Position = UDim2.new(math.random(), 0, math.random(), 0)
            p.ZIndex = 1
            p.Parent = folder

            local duration = 4 + math.random() * 4
            local tween = TweenService:Create(
                p,
                TweenInfo.new(duration, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
                {Position = UDim2.new(p.Position.X.Scale, 0, p.Position.Y.Scale + 0.06, 0)}
            )
            tween:Play()
            table.insert(particles, {frame = p, tween = tween})
        end

        return function()
            for _, item in ipairs(particles) do
                if item.tween then
                    pcall(function() item.tween:Cancel() end)
                end
            end
            if folder.Parent then
                folder:Destroy()
            end
        end
    end

    Themes["Sunset"] = {
        Theme = {
            Accent  = Color3.fromRGB(255, 160, 100),
            BG0     = Color3.fromRGB(22, 10, 8),
            BG1     = Color3.fromRGB(30, 14, 10),
            BG2     = Color3.fromRGB(38, 18, 12),
            Stroke  = Color3.fromRGB(100, 60, 40),
            Text    = Color3.fromRGB(255, 230, 210),
            Muted   = Color3.fromRGB(190, 150, 120),
            DimText = Color3.fromRGB(160, 120, 100),
            Track   = Color3.fromRGB(32, 18, 14),
            Field   = Color3.fromRGB(26, 12, 10),
        },
        Background = makeSunsetBackground,
    }

    -- Mono-Theme ohne Hintergrundanimation -----------------------------------

    Themes["Mono"] = {
        Theme = {
            Accent  = Color3.fromRGB(220, 220, 220),
            BG0     = Color3.fromRGB(12, 12, 12),
            BG1     = Color3.fromRGB(18, 18, 18),
            BG2     = Color3.fromRGB(24, 24, 24),
            Stroke  = Color3.fromRGB(80, 80, 80),
            Text    = Color3.fromRGB(240, 240, 240),
            Muted   = Color3.fromRGB(170, 170, 170),
            DimText = Color3.fromRGB(120, 120, 120),
            Track   = Color3.fromRGB(22, 22, 22),
            Field   = Color3.fromRGB(16, 16, 16),
        },
        Background = nil,
    }

    -- Ocean-Theme mit sanfter Welle ------------------------------------------

    local function makeOceanBackground(win)
        local holder = getHolder(win)
        if not holder then return end

        local folder = Instance.new("Folder")
        folder.Name = "Vanith_OceanBackground"
        folder.Parent = holder

        local base = Instance.new("Frame")
        base.Name = "Base"
        base.BorderSizePixel = 0
        base.BackgroundColor3 = Color3.fromRGB(6, 14, 26)
        base.BackgroundTransparency = 0.2
        base.Size = UDim2.fromScale(1, 1)
        base.ZIndex = 0
        base.Parent = folder

        local wave = Instance.new("Frame")
        wave.BorderSizePixel = 0
        wave.BackgroundColor3 = Color3.fromRGB(60, 130, 200)
        wave.BackgroundTransparency = 0.6
        wave.Size = UDim2.new(1, 0, 0, 80)
        wave.Position = UDim2.new(0, 0, 0.7, 0)
        wave.ZIndex = 1
        wave.Parent = folder

        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(1, 0)
        corner.Parent = wave

        local tween = TweenService:Create(
            wave,
            TweenInfo.new(6, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
            {Position = UDim2.new(0, 0, 0.62, 0)}
        )
        tween:Play()

        return function()
            pcall(function() tween:Cancel() end)
            if folder.Parent then
                folder:Destroy()
            end
        end
    end

    Themes["Ocean"] = {
        Theme = {
            Accent  = Color3.fromRGB(120, 190, 255),
            BG0     = Color3.fromRGB(6, 10, 18),
            BG1     = Color3.fromRGB(10, 16, 26),
            BG2     = Color3.fromRGB(14, 20, 32),
            Stroke  = Color3.fromRGB(50, 80, 120),
            Text    = Color3.fromRGB(230, 245, 255),
            Muted   = Color3.fromRGB(150, 175, 200),
            DimText = Color3.fromRGB(110, 135, 160),
            Track   = Color3.fromRGB(18, 24, 32),
            Field   = Color3.fromRGB(12, 18, 26),
        },
        Background = makeOceanBackground,
    }

    -- alle Themes bei Vanith registrieren ------------------------------------

    for name, def in pairs(Themes) do
        VanithRef:RegisterTheme(name, def)
    end
end

-- notify -------------------------------------------------------------

local notifyState = {
    Gui    = nil,
    Holder = nil,
    Scale  = nil,
}

local function EnsureNotifyRoot()
    if notifyState.Gui and notifyState.Gui.Parent then
        return notifyState
    end

    local parent = GetRootParent()
    if not parent then
        return notifyState
    end

    local gui = New("ScreenGui", {
        Name = "VanithNotify",
        ResetOnSpawn = false,
        IgnoreGuiInset = true,
        Parent = parent,
    })

    local holder = New("Frame", {
        BackgroundTransparency = 1,
        AnchorPoint = Vector2.new(1, 1),
        Position = UDim2.new(1, -24, 1, -24),
        Size = UDim2.new(0, 380, 0, 320),
        Parent = gui,
        ZIndex = 40,
    })

    local scale = New("UIScale", {Parent = holder})
    local cam = workspace.CurrentCamera
    if cam then
        local vp = cam.ViewportSize
        if vp.X < 900 then
            scale.Scale = math.clamp(vp.X / 900, 0.8, 1)
        else
            scale.Scale = 1
        end
    else
        scale.Scale = 1
    end

    local layout = New("UIListLayout", {
        FillDirection = Enum.FillDirection.Vertical,
        SortOrder     = Enum.SortOrder.LayoutOrder,
        Padding       = UDim.new(0, 10),
        Parent        = holder,
    })
    layout.VerticalAlignment   = Enum.VerticalAlignment.Bottom
    layout.HorizontalAlignment = Enum.HorizontalAlignment.Right

    notifyState.Gui    = gui
    notifyState.Holder = holder
    notifyState.Scale  = scale

    return notifyState
end

local function DoNotify(theme, cfg)
    cfg = cfg or {}

    local state = EnsureNotifyRoot()
    if not state.Holder then
        return
    end

    local title    = cfg.Title or "Vanith"
    local message  = cfg.Text or cfg.Message or ""
    local duration = tonumber(cfg.Duration) or 4

    local accent   = cfg.AccentColor     or (theme and theme.Accent) or DEFAULT_THEME.Accent
    local bg       = cfg.BackgroundColor or (theme and theme.BG1)    or DEFAULT_THEME.BG1
    local textCol  = cfg.TextColor       or (theme and theme.Text)   or DEFAULT_THEME.Text
    local mutedCol = cfg.MutedColor      or (theme and theme.Muted)  or DEFAULT_THEME.Muted

    local card = New("Frame", {
        BackgroundColor3       = bg,
        BackgroundTransparency = 1,
        Size                   = UDim2.new(1, 0, 0, 0),
        AutomaticSize          = Enum.AutomaticSize.Y,
        Parent                 = state.Holder,
        ZIndex                 = 41,
    })
    New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = card})
    local st = New("UIStroke", {Thickness = 1.2, Transparency = 0.2, Parent = card})
    st.Color = accent

    New("UIPadding", {
        PaddingTop    = UDim.new(0, 12),
        PaddingBottom = UDim.new(0, 12),
        PaddingLeft   = UDim.new(0, 14),
        PaddingRight  = UDim.new(0, 14),
        Parent        = card,
    })

    local titleLbl = New("TextLabel", {
        BackgroundTransparency = 1,
        Font                   = Enum.Font.GothamBold,
        Text                   = title,
        TextSize               = 14,
        TextXAlignment         = Enum.TextXAlignment.Left,
        Size                   = UDim2.new(1, 0, 0, 20),
        Parent                 = card,
        ZIndex                 = 42,
    })
    titleLbl.TextColor3 = accent

    local msgLbl = New("TextLabel", {
        BackgroundTransparency = 1,
        Font                   = Enum.Font.Gotham,
        Text                   = message,
        TextSize               = 13,
        TextWrapped            = true,
        TextXAlignment         = Enum.TextXAlignment.Left,
        TextYAlignment         = Enum.TextYAlignment.Top,
        Position               = UDim2.new(0, 0, 0, 24),
        Size                   = UDim2.new(1, 0, 0, 0),
        AutomaticSize          = Enum.AutomaticSize.Y,
        Parent                 = card,
        ZIndex                 = 42,
    })
    msgLbl.TextColor3 = textCol

    card.BackgroundTransparency   = 1
    titleLbl.TextTransparency     = 1
    msgLbl.TextTransparency       = 1

    PlayTween(card,     {BackgroundTransparency = 0}, 0.2)
    PlayTween(titleLbl, {TextTransparency = 0}, 0.2)
    PlayTween(msgLbl,   {TextTransparency = 0}, 0.2)

    local closed = false
    local function close()
        if closed or not card.Parent then return end
        closed = true
        PlayTween(card,     {BackgroundTransparency = 1}, 0.2)
        PlayTween(titleLbl, {TextTransparency = 1}, 0.2)
        PlayTween(msgLbl,   {TextTransparency = 1}, 0.2)
        task.delay(0.22, function()
            if card.Parent then
                card:Destroy()
            end
        end)
    end

    task.delay(duration, close)

    card.InputBegan:Connect(function(input)
        if IsMouse(input.UserInputType) or IsTouch(input.UserInputType) then
            close()
        end
    end)

    return close
end

function Vanith:Notify(cfg)
    return DoNotify(nil, cfg)
end

function Window:Notify(cfg)
    return DoNotify(self.Theme, cfg)
end

-- window helpers -----------------------------------------------------

function Window:_track(inst, prop, key)
    table.insert(self._themeBindings, {inst = inst, prop = prop, key = key})
    inst[prop] = self.Theme[key]
    return inst
end

function Window:ApplyTheme()
    for _, b in ipairs(self._themeBindings) do
        if b.inst and b.inst.Parent then
            b.inst[b.prop] = self.Theme[b.key]
        end
    end
end

function Window:SetTheme(tbl)
    for k, v in pairs(tbl or {}) do
        self.Theme[k] = v
    end
    self:ApplyTheme()
end

-- wendet ein registriertes Theme + optionalen Hintergrund an
function Window:SetThemePreset(name)
    if type(name) ~= "string" or name == "" then return end

    local def = Vanith.RegisteredThemes and Vanith.RegisteredThemes[name]
    if not def then
        return
    end

    if type(def.Theme) == "table" then
        self:SetTheme(def.Theme)
    end

    if self._backgroundCleanup then
        pcall(self._backgroundCleanup)
        self._backgroundCleanup = nil
    end

    if type(def.Background) == "function" then
        local ok, cleanup = pcall(def.Background, self)
        if ok and type(cleanup) == "function" then
            self._backgroundCleanup = cleanup
        end
    end

    self.CurrentThemePreset = name
end

function Window:SetAccent(color)
    self.Theme.Accent = color
    self:ApplyTheme()
end

function Window:SetVisible(state)
    state = state and true or false
    self.IsMinimized = not state

    if self.Main then
        self.Main.Visible = state
    end
    if self.MiniButton then
        self.MiniButton.Visible = not state
    end
end

function Window:Minimize()
    self:SetVisible(false)
end

function Window:Restore()
    self:SetVisible(true)
end

function Window:Destroy()
    if self._backgroundCleanup then
        pcall(self._backgroundCleanup)
        self._backgroundCleanup = nil
    end
    if self.Gui then
        self.Gui:Destroy()
    end
end

-- drag ---------------------------------------------------------------

function Window:_enableDrag(handle, target)
    local dragging
    local dragStart
    local startPos
    local activeInput

    local function update(input)
        if not dragging then return end
        local delta = input.Position - dragStart
        target.Position = UDim2.new(
            startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y
        )
    end

    handle.InputBegan:Connect(function(input)
        if IsMouse(input.UserInputType) or IsTouch(input.UserInputType) then
            dragging    = true
            activeInput = input
            dragStart   = input.Position
            startPos    = target.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging    = false
                    activeInput = nil
                end
            end)
        end
    end)

    handle.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            activeInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == activeInput then
            update(input)
        end
    end)
end

-- resize -------------------------------------------------------------

function Window:_enableResize(handle, target)
    local minSize = self.MinSize or Vector2.new(649, 417)

    local resizing
    local dragStart
    local startSize
    local activeInput

    local function update(input)
        if not resizing then return end
        local delta = input.Position - dragStart
        local newSize = Vector2.new(
            math.max(minSize.X, startSize.X + delta.X),
            math.max(minSize.Y, startSize.Y + delta.Y)
        )
        target.Size = UDim2.fromOffset(newSize.X, newSize.Y)
    end

    handle.InputBegan:Connect(function(input)
        if IsMouse(input.UserInputType) or IsTouch(input.UserInputType) then
            resizing    = true
            activeInput = input
            dragStart   = input.Position
            startSize   = Vector2.new(target.AbsoluteSize.X, target.AbsoluteSize.Y)

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    resizing    = false
                    activeInput = nil
                end
            end)
        end
    end)

    handle.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            activeInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == activeInput then
            update(input)
        end
    end)
end

-- top strip tab button -----------------------------------------------

function Window:_makeTopTabButton(text)
    local holder = self.TopTabsHolder

    -- Feste Breite, damit ALLE TopTabs gleich groß sind
    local btn = New("TextButton", {
        AutoButtonColor       = false,
        BackgroundTransparency = 1,
        Text                  = "",
        Size                  = UDim2.new(0, 72, 1, 0), -- << fixed width
        Parent                = holder,
        ZIndex                = 4,
    })

    local label = New("TextLabel", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 1, 0),
        Font = Enum.Font.GothamBold,
        Text = text,
        TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Center,
        Parent = btn,
        ZIndex = 5,
        TextColor3 = self.Theme.DimText,
    })
    -- kein AutoFit hier

    local underline = New("Frame", {
        BorderSizePixel = 0,
        Size = UDim2.new(0.7, 0, 0, 2),
        Position = UDim2.new(0.15, 0, 1, -3),
        Parent = btn,
        Visible = false,
        ZIndex = 4,
        BackgroundColor3 = self.Theme.Accent,
    })

    local t = {Button = btn, Label = label, Underline = underline, Tab = nil}
    table.insert(self.TopTabs, t)
    return t
end

-- left side tabs -----------------------------------------------------

function Window:CreateTab(name)
    local row = New("TextButton", {
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        Text = "",
        Size = UDim2.new(1, -20, 0, 38),
        Parent = self.SideList,
        ZIndex = 2,
    })

    local bg = New("Frame", {
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 1, 0),
        Parent = row,
        ZIndex = 1,
    })
    self:_track(bg, "BackgroundColor3", "BG2")

    local accent = New("Frame", {
        BorderSizePixel = 0,
        Size = UDim2.new(0, 2, 0.6, 0),
        Position = UDim2.new(0, 0, 0.2, 0),
        Parent = row,
        Visible = false,
        ZIndex = 2,
    })
    self:_track(accent, "BackgroundColor3", "Accent")

    local label = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.fromOffset(10, 8),
        Size = UDim2.new(1, -20, 0, 22),
        Font = Enum.Font.GothamBold,
        Text = name,
        TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = row,
        ZIndex = 3,
    })
    self:_track(label, "TextColor3", "Muted")
    -- kein AutoFit hier

    local page = New("Frame", {
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 1, 0),
        Visible = false,
        Parent = self.Pages,
        ZIndex = 1,
    })

    local tab = setmetatable({
        Window        = self,
        Name          = name,
        Row           = row,
        Label         = label,
        Accent        = accent,
        Bg            = bg,
        Page          = page,
        _sections     = {},
        _topTabs      = {},
        _activeTopTab = nil,
    }, Tab)

    row.MouseButton1Click:Connect(function()
        self:SelectTab(tab)
    end)

    table.insert(self.Tabs, tab)
    if #self.Tabs == 1 then
        self:SelectTab(tab)
    end

    return tab
end

function Window:SelectTab(tab)
    self.ActiveTab = tab

    for _, t in ipairs(self.Tabs) do
        local active = (t == tab)

        t.Page.Visible = active
        PlayTween(t.Bg, {BackgroundTransparency = active and 0 or 1}, 0.12)
        t.Accent.Visible = active
        PlayTween(t.Label, {TextColor3 = active and self.Theme.Text or self.Theme.Muted}, 0.12)

        for _, topTab in ipairs(t._topTabs) do
            topTab.Button.Visible = active
        end
    end

    if tab._activeTopTab then
        tab:SetTopTab(tab._activeTopTab)
    end
end

-- tab methods / sections ---------------------------------------------

function Tab:CreateTopTab(name)
    local win = self.Window
    local tt  = win:_makeTopTabButton(name)
    tt.Tab    = self

    table.insert(self._topTabs, tt)
    tt.Button.Visible = (win.ActiveTab == self)

    tt.Button.MouseButton1Click:Connect(function()
        self:SetTopTab(tt)
    end)

    if not self._activeTopTab then
        self:SetTopTab(tt)
    end

    return tt
end

function Tab:SetTopTabByName(name)
    for _, tt in ipairs(self._topTabs) do
        if tt.Label and tt.Label.Text == name then
            self:SetTopTab(tt)
            break
        end
    end
end

function Tab:SetTopTab(tt)
    self._activeTopTab = tt
    local win = self.Window

    for _, other in ipairs(self._topTabs) do
        local active = (other == tt)
        other.Underline.Visible = active and (win.ActiveTab == self)
        PlayTween(other.Label, {
            TextColor3 = active and win.Theme.Accent or win.Theme.DimText
        }, 0.12)
    end

    for _, sec in ipairs(self._sections) do
        if sec._topTab == nil then
            sec.Frame.Visible = true
        else
            sec.Frame.Visible = (sec._topTab == tt)
        end
    end

    for _, sec in ipairs(self._sections) do
        if sec._controls then
            for _, ctrl in ipairs(sec._controls) do
                if ctrl._syncProfile then
                    ctrl:_syncProfile()
                end
            end
        end
    end
end

function Tab:GetActiveTopTabName()
    return self._activeTopTab and self._activeTopTab.Label.Text or nil
end

-- slot: "LeftTop", "LeftBottom", "RightTop", "RightBottom"

function Tab:CreateSection(title, slot, topTabNameOrObj)
    local win  = self.Window
    local page = self.Page

    local layout = {
        LeftTop = {
            Pos  = UDim2.new(0, 0, 0, 0),
            Size = UDim2.new(0.7, -6, 0.5, -6),
        },
        LeftBottom = {
            Pos  = UDim2.new(0, 0, 0.5, 6),
            Size = UDim2.new(0.7, -6, 0.5, -6),
        },
        RightTop = {
            Pos  = UDim2.new(0.7, 6, 0, 0),
            Size = UDim2.new(0.3, -6, 0.5, -6),
        },
        RightBottom = {
            Pos  = UDim2.new(0.7, 6, 0.5, 6),
            Size = UDim2.new(0.3, -6, 0.5, -6),
        },
    }

    local info  = layout[slot or ""]
    local props = {BorderSizePixel = 0, Parent = page, ClipsDescendants = true}

    if info then
        props.Position = info.Pos
        props.Size     = info.Size
    else
        local idx = #self._sections
        props.Position = UDim2.fromOffset(0, idx * 196)
        props.Size     = UDim2.new(1, 0, 0, 180)
    end

    local frame = New("Frame", props)
    win:_track(frame, "BackgroundColor3", "BG1")
    New("UICorner", {CornerRadius = UDim.new(0, 8), Parent = frame})
    local stroke = New("UIStroke", {Thickness = 1, Transparency = 0.25, Parent = frame})
    win:_track(stroke, "Color", "Stroke")

    local head = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.fromOffset(12, 10),
        Size = UDim2.new(1, -24, 0, 18),
        Font = Enum.Font.GothamBold,
        Text = title,
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = frame,
    })
    win:_track(head, "TextColor3", "Accent")
    -- kein AutoFit

    local inner = New("ScrollingFrame", {
        BorderSizePixel = 0,
        BackgroundTransparency = 1,
        Position = UDim2.fromOffset(0, 34),
        Size = UDim2.new(1, 0, 1, -34),
        CanvasSize = UDim2.new(0, 0, 0, 0),
        ScrollBarThickness = 3,
        ScrollBarImageTransparency = 0.3,
        ScrollBarImageColor3 = win.Theme.Accent,
        ScrollingDirection = Enum.ScrollingDirection.Y,
        AutomaticCanvasSize = Enum.AutomaticSize.Y,
        Parent = frame,
    })

    local list = New("UIListLayout", {
        Padding = UDim.new(0, 6),
        SortOrder = Enum.SortOrder.LayoutOrder,
        Parent = inner,
    })
    New("UIPadding", {
        PaddingTop = UDim.new(0, 6),
        PaddingLeft = UDim.new(0, 12),
        PaddingRight = UDim.new(0, 12),
        PaddingBottom = UDim.new(0, 10),
        Parent = inner,
    })

    list:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        inner.CanvasSize = UDim2.new(0, 0, 0, list.AbsoluteContentSize.Y + 16)
    end)

    local associatedTopTab = nil
    if topTabNameOrObj ~= nil then
        if typeof(topTabNameOrObj) == "string" then
            for _, tt in ipairs(self._topTabs) do
                if tt.Label and tt.Label.Text == topTabNameOrObj then
                    associatedTopTab = tt
                    break
                end
            end
        elseif type(topTabNameOrObj) == "table" and topTabNameOrObj.Label then
            associatedTopTab = topTabNameOrObj
        end
    end

    if associatedTopTab and self._activeTopTab and self._activeTopTab ~= associatedTopTab then
        frame.Visible = false
    end

    local sec = setmetatable({
        Window    = win,
        Tab       = self,
        Frame     = frame,
        Inner     = inner,
        List      = list,
        _controls = {},
        _topTab   = associatedTopTab,
    }, Section)

    table.insert(self._sections, sec)

    return sec
end

-- controls: slider ---------------------------------------------------

function Section:CreateSlider(cfg)
    local win   = self.Window
    local inner = self.Inner
    local tab   = self.Tab
    local c     = cfg or {}

    local minv  = c.Min or 0
    local maxv  = c.Max or 100
    local step  = c.Step or 1

    local function getProfileKey()
        if tab and tab._activeTopTab then
            return tab._activeTopTab
        end
        return "__default"
    end

    local valuesByProfile = {}

    local defaultValue = RoundStep(math.clamp(c.Default or minv, minv, maxv), minv, step)
    local value        = defaultValue
    valuesByProfile[getProfileKey()] = defaultValue

    local row = New("Frame", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 0, 54),
        Parent = inner,
    })

    local label = New("TextLabel", {
        BackgroundTransparency = 1,
        Size = UDim2.new(0.6, 0, 0, 18),
        Font = Enum.Font.Gotham,
        Text = c.Name or "Slider",
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = row,
    })
    win:_track(label, "TextColor3", "DimText")
    -- Slider-Label auto-fit
    AutoFitText(label, 11, 8, 4)

    local valueText = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0.6, 0, 0, 0),
        Size = UDim2.new(0.4, 0, 0, 18),
        Font = Enum.Font.GothamBold,
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Right,
        Parent = row,
    })
    win:_track(valueText, "TextColor3", "Accent")
    -- Slider-Wert auto-fit
    AutoFitText(valueText, 11, 8, 4)

    local track = New("Frame", {
        BorderSizePixel = 0,
        Position = UDim2.fromOffset(0, 26),
        Size = UDim2.new(1, 0, 0, 4),
        Parent = row,
    })
    win:_track(track, "BackgroundColor3", "Track")
    New("UICorner", {CornerRadius = UDim.new(0, 2), Parent = track})

    local fill = New("Frame", {
        BorderSizePixel = 0,
        Size = UDim2.new(0, 0, 1, 0),
        Parent = track,
    })
    win:_track(fill, "BackgroundColor3", "Accent")
    New("UICorner", {CornerRadius = UDim.new(0, 2), Parent = fill})

    local knob = New("Frame", {
        BorderSizePixel = 0,
        Size = UDim2.fromOffset(10, 10),
        Parent = track,
    })
    win:_track(knob, "BackgroundColor3", "Muted")
    New("UICorner", {CornerRadius = UDim.new(0, 2), Parent = knob})

    local function setVisual(v, tweenIt)
        local t = (v - minv) / (maxv - minv)
        t = Clamp01(t)

        local fillSize = UDim2.new(t, 0, 1, 0)
        local knobPos  = UDim2.new(t, -5, 0.5, -5)

        if tweenIt then
            PlayTween(fill, {Size = fillSize}, 0.12)
            PlayTween(knob, {Position = knobPos}, 0.12)
        else
            fill.Size     = fillSize
            knob.Position = knobPos
        end

        local text = c.Format and c.Format(v) or tostring(v)
        valueText.Text = text
    end

    local function syncProfile()
        local key = getProfileKey()
        local v   = valuesByProfile[key]
        if v == nil then
            v = defaultValue
            valuesByProfile[key] = v
        end
        value = v
        setVisual(v, false)
    end

    local function setValue(v, fromUser)
        v = RoundStep(math.clamp(v, minv, maxv), minv, step)
        value = v
        valuesByProfile[getProfileKey()] = v
        setVisual(v, true)
        if fromUser and c.Callback then
            task.spawn(c.Callback, v)
        end
    end

    syncProfile()

    local dragging

    local function valueFromInput(input)
        local x = input.Position.X - track.AbsolutePosition.X
        local ratio = Clamp01(x / math.max(track.AbsoluteSize.X, 1))
        return minv + ratio * (maxv - minv)
    end

    local function begin(input)
        dragging = true
        PlayTween(knob, {BackgroundColor3 = win.Theme.Accent}, 0.1)
        setValue(valueFromInput(input), true)

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
                PlayTween(knob, {BackgroundColor3 = win.Theme.Muted}, 0.1)
            end
        end)
    end

    row.InputBegan:Connect(function(input)
        if IsMouse(input.UserInputType) or IsTouch(input.UserInputType) then
            begin(input)
        end
    end)

    track.InputBegan:Connect(function(input)
        if IsMouse(input.UserInputType) or IsTouch(input.UserInputType) then
            begin(input)
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if not dragging then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            setValue(valueFromInput(input), true)
        end
    end)

    local obj = {}
    function obj:Set(v)
        v = RoundStep(math.clamp(v, minv, maxv), minv, step)
        value = v
        valuesByProfile[getProfileKey()] = v
        setVisual(v, false)
    end
    function obj:Get()
        return value
    end
    function obj:_syncProfile()
        syncProfile()
    end
    obj._profileValues = valuesByProfile

    table.insert(self._controls, obj)
    return obj
end

-- controls: toggle ---------------------------------------------------

function Section:CreateToggle(cfg)
    local win   = self.Window
    local inner = self.Inner
    local tab   = self.Tab
    local c     = cfg or {}

    local function getProfileKey()
        if tab and tab._activeTopTab then
            return tab._activeTopTab
        end
        return "__default"
    end

    local stateByProfile = {}
    local defaultState   = c.Default and true or false
    stateByProfile[getProfileKey()] = defaultState
    local state = defaultState

    local small = c.Small or false

    local row = New("TextButton", {
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        Text = "",
        Size = UDim2.new(1, 0, 0, 24),
        Parent = inner,
    })

    local label = New("TextLabel", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, -70, 1, 0),
        Font = Enum.Font.Gotham,
        Text = c.Name or "Toggle",
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = row,
    })
    win:_track(label, "TextColor3", "Text")
    -- kein AutoFit

    local obj = {}

    if small then
        local box = New("Frame", {
            BorderSizePixel = 0,
            Size = UDim2.fromOffset(16, 16),
            Position = UDim2.new(1, -24, 0.5, -8),
            Parent = row,
        })
        win:_track(box, "BackgroundColor3", "Field")
        New("UICorner", {CornerRadius = UDim.new(0, 3), Parent = box})
        local st = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = box})
        win:_track(st, "Color", "Stroke")

        local fill = New("Frame", {
            BorderSizePixel = 0,
            Size = UDim2.new(1, -4, 1, -4),
            Position = UDim2.fromOffset(2, 2),
            Parent = box,
        })
        New("UICorner", {CornerRadius = UDim.new(0, 2), Parent = fill})

        local function render(animated)
            local col = state and win.Theme.Accent or win.Theme.Track
            if animated then
                PlayTween(fill, {BackgroundColor3 = col}, 0.12)
            else
                fill.BackgroundColor3 = col
            end
        end

        local function syncProfile()
            local key = getProfileKey()
            local s   = stateByProfile[key]
            if s == nil then
                s = defaultState
                stateByProfile[key] = s
            end
            state = s
            render(false)
        end

        syncProfile()

        row.MouseButton1Click:Connect(function()
            state = not state
            stateByProfile[getProfileKey()] = state
            render(true)
            if c.Callback then
                task.spawn(c.Callback, state)
            end
        end)

        function obj:Set(v)
            state = v and true or false
            stateByProfile[getProfileKey()] = state
            render(false)
        end
        function obj:Get()
            return state
        end
        function obj:_syncProfile()
            syncProfile()
        end
        obj._profileValues = stateByProfile
    else
        local sw = New("Frame", {
            BorderSizePixel = 0,
            Size = UDim2.fromOffset(28, 18),
            Position = UDim2.new(1, -36, 0.5, -9),
            Parent = row,
        })
        win:_track(sw, "BackgroundColor3", "Field")
        New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = sw})
        local st = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = sw})
        win:_track(st, "Color", "Stroke")

        local fill = New("Frame", {
            BorderSizePixel = 0,
            Size = UDim2.new(1, -4, 1, -4),
            Position = UDim2.fromOffset(2, 2),
            Parent = sw,
        })
        New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = fill})

        local knob = New("Frame", {
            BorderSizePixel = 0,
            Size = UDim2.fromOffset(12, 12),
            Parent = sw,
        })
        New("UICorner", {CornerRadius = UDim.new(1, 0), Parent = knob})

        local function render(animated)
            local onCol  = win.Theme.Accent
            local offCol = win.Theme.Track
            local posOn  = UDim2.fromOffset(13, 3)
            local posOff = UDim2.fromOffset(3, 3)
            local col    = state and onCol or offCol

            if animated then
                PlayTween(fill, {BackgroundColor3 = col}, 0.12)
                PlayTween(knob, {
                    Position = state and posOn or posOff,
                    BackgroundColor3 = Color3.new(1, 1, 1),
                }, 0.12)
            else
                fill.BackgroundColor3 = col
                knob.Position         = state and posOn or posOff
                knob.BackgroundColor3 = Color3.new(1, 1, 1)
            end
        end

        local function syncProfile()
            local key = getProfileKey()
            local s   = stateByProfile[key]
            if s == nil then
                s = defaultState
                stateByProfile[key] = s
            end
            state = s
            render(false)
        end

        syncProfile()

        row.MouseButton1Click:Connect(function()
            state = not state
            stateByProfile[getProfileKey()] = state
            render(true)
            if c.Callback then
                task.spawn(c.Callback, state)
            end
        end)

        function obj:Set(v)
            state = v and true or false
            stateByProfile[getProfileKey()] = state
            render(false)
        end
        function obj:Get()
            return state
        end
        function obj:_syncProfile()
            syncProfile()
        end
        obj._profileValues = stateByProfile
    end

    table.insert(self._controls, obj)
    return obj
end

-- controls: button ---------------------------------------------------

function Section:CreateButton(cfg)
    local win   = self.Window
    local inner = self.Inner
    local c     = cfg or {}

    local btn = New("TextButton", {
        AutoButtonColor = false,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 24),
        Font = Enum.Font.GothamBold,
        Text = c.Name or "Button",
        TextSize = 11,
        Parent = inner,
    })
    win:_track(btn, "BackgroundColor3", "Field")
    win:_track(btn, "TextColor3", "Text")
    New("UICorner", {CornerRadius = UDim.new(0, 3), Parent = btn})
    local st = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = btn})
    win:_track(st, "Color", "Stroke")
    -- kein AutoFit

    btn.MouseButton1Click:Connect(function()
        PlayTween(btn, {BackgroundColor3 = win.Theme.Track}, 0.08)
        if c.Callback then
            task.spawn(c.Callback)
        end
        task.delay(0.08, function()
            if btn.Parent then
                PlayTween(btn, {BackgroundColor3 = win.Theme.Field}, 0.12)
            end
        end)
    end)

    return btn
end

-- controls: textbox --------------------------------------------------

function Section:CreateTextbox(cfg)
    local win   = self.Window
    local inner = self.Inner
    local tab   = self.Tab
    local c     = cfg or {}

    local function getProfileKey()
        if tab and tab._activeTopTab then
            return tab._activeTopTab
        end
        return "__default"
    end

    local valuesByProfile = {}
    local defaultText     = c.Default or ""
    valuesByProfile[getProfileKey()] = defaultText

    local box = New("TextBox", {
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 24),
        Font = Enum.Font.Gotham,
        Text = defaultText,
        PlaceholderText = c.Placeholder or "Enter text",
        TextSize = 11,
        ClearTextOnFocus = false,
        Parent = inner,
    })
    win:_track(box, "BackgroundColor3", "Field")
    win:_track(box, "TextColor3", "Text")
    box.PlaceholderColor3 = win.Theme.Muted
    New("UICorner", {CornerRadius = UDim.new(0, 3), Parent = box})
    local st = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = box})
    win:_track(st, "Color", "Stroke")

    local function syncProfile()
        local key = getProfileKey()
        local t   = valuesByProfile[key]
        if t == nil then
            t = defaultText
            valuesByProfile[key] = t
        end
        box.Text = t
    end

    syncProfile()

    box.FocusLost:Connect(function(enterPressed)
        valuesByProfile[getProfileKey()] = box.Text
        if c.Callback then
            task.spawn(c.Callback, box.Text, enterPressed)
        end
    end)

    local obj = {}
    function obj:SetText(t)
        t = t or ""
        valuesByProfile[getProfileKey()] = t
        box.Text = t
    end
    function obj:GetText()
        return box.Text
    end
    function obj:_syncProfile()
        syncProfile()
    end
    obj._profileValues = valuesByProfile

    table.insert(self._controls, obj)
    return obj
end

-- controls: dropdown (single + multi) -------------------------------

function Section:CreateDropdown(cfg)
    local win   = self.Window
    local inner = self.Inner
    local tab   = self.Tab
    local c     = cfg or {}

    local isMulti = c.Multi == true

    local function getProfileKey()
        if tab and tab._activeTopTab then
            return tab._activeTopTab
        end
        return "__default"
    end

    local options           = c.Options or {}
    local selectedByProfile = {}
    local maxHeight         = c.MaxHeight

    local ROW_HEIGHT   = 24
    local ITEM_HEIGHT  = 20
    local ITEM_PADDING = 4
    local TOP_BOTTOM   = 8

    local function calcContentHeight()
        local count = #options
        if count <= 0 then return 0 end
        local buttonsHeight = count * ITEM_HEIGHT
        local spacing       = (count - 1) * ITEM_PADDING
        return buttonsHeight + spacing + TOP_BOTTOM
    end

    local wrap = New("Frame", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
        Parent = inner,
    })

    local head = New("TextButton", {
        AutoButtonColor = false,
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, ROW_HEIGHT),
        Text = "",
        Parent = wrap,
    })
    win:_track(head, "BackgroundColor3", "Field")
    New("UICorner", {CornerRadius = UDim.new(0, 3), Parent = head})
    local st = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = head})
    win:_track(st, "Color", "Stroke")

    local nameLbl = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.fromOffset(10, 0),
        Size = UDim2.new(0.6, -10, 1, 0),
        Font = Enum.Font.Gotham,
        Text = c.Name or "Dropdown",
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = head,
    })
    win:_track(nameLbl, "TextColor3", "DimText")
    -- kein AutoFit

    local valLbl = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0.6, 0, 0, 0),
        Size = UDim2.new(0.4, -26, 1, 0),
        Font = Enum.Font.GothamBold,
        Text = "",
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Right,
        Parent = head,
    })
    win:_track(valLbl, "TextColor3", "Text")
    -- kein AutoFit

    local arrow = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(1, -22, 0, 0),
        Size = UDim2.fromOffset(20, ROW_HEIGHT),
        Font = Enum.Font.GothamBold,
        Text = "<",
        TextSize = 14,
        Parent = head,
    })
    win:_track(arrow, "TextColor3", "Muted")

    local listWrap = New("Frame", {
        BackgroundTransparency = 1,
        Position = UDim2.fromOffset(0, ROW_HEIGHT),
        Size = UDim2.new(1, 0, 0, 0),
        ClipsDescendants = true,
        Parent = wrap,
    })

    local listBg = New("Frame", {
        BorderSizePixel        = 0,
        Size                   = UDim2.new(1, 0, 0, 0),
        BackgroundTransparency = 0,
        Parent                 = listWrap,
    })
    win:_track(listBg, "BackgroundColor3", "Field")
    New("UICorner", {CornerRadius = UDim.new(0, 3), Parent = listBg})
    local st2 = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = listBg})
    win:_track(st2, "Color", "Stroke")

    local list = New("UIListLayout", {
        SortOrder = Enum.SortOrder.LayoutOrder,
        Padding   = UDim.new(0, ITEM_PADDING),
        Parent    = listBg,
    })
    New("UIPadding", {
        PaddingTop    = UDim.new(0, TOP_BOTTOM/2),
        PaddingLeft   = UDim.new(0, 10),
        PaddingRight  = UDim.new(0, 10),
        PaddingBottom = UDim.new(0, TOP_BOTTOM/2),
        Parent        = listBg,
    })

    local function getOrInitProfileValue()
        local key = getProfileKey()
        local v   = selectedByProfile[key]

        if isMulti then
            if type(v) ~= "table" or v.__multi ~= true or type(v.values) ~= "table" then
                local map = {}
                if type(c.Default) == "table" then
                    for _, opt in ipairs(c.Default) do
                        if table.find(options, opt) then
                            map[opt] = true
                        end
                    end
                elseif c.Default ~= nil then
                    if table.find(options, c.Default) then
                        map[c.Default] = true
                    end
                end
                v = { __multi = true, values = map }
                selectedByProfile[key] = v
            end
        else
            if v == nil or type(v) ~= "string" then
                local d = c.Default or options[1] or "None"
                v = d
                selectedByProfile[key] = v
            end
        end

        return v
    end

    local function getDisplayText()
        if isMulti then
            local entry = getOrInitProfileValue()
            local map   = entry.values or {}
            local listVals  = {}

            for _, opt in ipairs(options) do
                if map[opt] then
                    table.insert(listVals, tostring(opt))
                end
            end

            if #listVals == 0 then
                return "None"
            elseif #listVals <= 2 then
                return table.concat(listVals, ", ")
            else
                return tostring(#listVals) .. " selected"
            end
        else
            local v = getOrInitProfileValue()
            return tostring(v)
        end
    end

    local function fireCallback()
        if not c.Callback then return end

        if isMulti then
            local entry = getOrInitProfileValue()
            local map   = entry.values or {}
            local out   = {}
            for _, opt in ipairs(options) do
                if map[opt] then
                    table.insert(out, opt)
                end
            end
            task.spawn(c.Callback, out)
        else
            task.spawn(c.Callback, getOrInitProfileValue())
        end
    end

    local function cleanupSelections()
        if isMulti then
            for key, entry in pairs(selectedByProfile) do
                if type(entry) ~= "table" or entry.__multi ~= true or type(entry.values) ~= "table" then
                    selectedByProfile[key] = nil
                else
                    local newMap = {}
                    for _, opt in ipairs(options) do
                        if entry.values[opt] then
                            newMap[opt] = true
                        end
                    end
                    entry.values = newMap
                end
            end
        else
            for key, val in pairs(selectedByProfile) do
                if not table.find(options, val) then
                    selectedByProfile[key] = options[1] or "None"
                end
            end
        end
    end

    local open = false

    local function setOpen(state)
        if state == open then return end
        open = state

        if open then
            arrow.Text = "v"
            local contentHeight = calcContentHeight()
            if maxHeight and maxHeight > 0 then
                contentHeight = math.min(contentHeight, maxHeight)
            end

            local targetSize = UDim2.new(1, 0, 0, contentHeight)

            PlayTween(listBg,   {Size = targetSize}, 0.12)
            PlayTween(listWrap, {Size = targetSize}, 0.12)
            PlayTween(wrap,     {Size = UDim2.new(1, 0, 0, ROW_HEIGHT + contentHeight)}, 0.12)
            PlayTween(arrow,    {Rotation = 90}, 0.12)
        else
            arrow.Text = "<"
            local closedSize = UDim2.new(1, 0, 0, 0)
            PlayTween(listBg,   {Size = closedSize}, 0.12)
            PlayTween(listWrap, {Size = closedSize}, 0.12)
            PlayTween(wrap,     {Size = UDim2.new(1, 0, 0, ROW_HEIGHT)}, 0.12)
            PlayTween(arrow,    {Rotation = 0}, 0.12)
        end
    end

    local function rebuild()
        for _, child in ipairs(listBg:GetChildren()) do
            if child:IsA("TextButton") then
                child:Destroy()
            end
        end

        for _, opt in ipairs(options) do
            local b = New("TextButton", {
                AutoButtonColor = false,
                BackgroundTransparency = 1,
                Size = UDim2.new(1, 0, 0, ITEM_HEIGHT),
                Font = Enum.Font.Gotham,
                Text = tostring(opt),
                TextSize = 11,
                TextXAlignment = Enum.TextXAlignment.Left,
                Parent = listBg,
            })
            b.TextColor3 = win.Theme.Text

            if isMulti then
                local function updateButton()
                    local entry = getOrInitProfileValue()
                    local map   = entry.values or {}
                    local sel   = map[opt] and true or false
                    b.Text = (sel and "● " or "○ ") .. tostring(opt)
                    b.TextColor3 = sel and win.Theme.Accent or win.Theme.Text
                end

                updateButton()

                b.MouseButton1Click:Connect(function()
                    local entry = getOrInitProfileValue()
                    local map   = entry.values
                    if map[opt] then
                        map[opt] = nil
                    else
                        map[opt] = true
                    end
                    updateButton()
                    valLbl.Text = getDisplayText()
                    fireCallback()
                    -- Multi bleibt offen
                end)
            else
                local function updateButton()
                    local current = getOrInitProfileValue()
                    b.TextColor3 = (current == opt) and win.Theme.Accent or win.Theme.Text
                end

                updateButton()

                b.MouseButton1Click:Connect(function()
                    selectedByProfile[getProfileKey()] = opt
                    valLbl.Text = tostring(opt)
                    fireCallback()
                    rebuild()
                    -- Single: schließen nach Klick
                    setOpen(false)
                end)
            end
        end
    end

    head.MouseButton1Click:Connect(function()
        setOpen(not open)
    end)

    head.MouseEnter:Connect(function()
        PlayTween(head, {BackgroundColor3 = win.Theme.Track}, 0.08)
    end)
    head.MouseLeave:Connect(function()
        PlayTween(head, {BackgroundColor3 = win.Theme.Field}, 0.12)
    end)

    local function syncProfile()
        cleanupSelections()
        getOrInitProfileValue()
        valLbl.Text = getDisplayText()
        rebuild()
    end

    syncProfile()

    local obj = {}
    function obj:SetOptions(opts)
        options = opts or {}
        cleanupSelections()
        valLbl.Text = getDisplayText()
        rebuild()
        setOpen(false)
    end

    function obj:Set(v)
        if isMulti then
            local map = {}
            if type(v) == "table" then
                for _, opt in ipairs(v) do
                    if table.find(options, opt) then
                        map[opt] = true
                    end
                end
            elseif v ~= nil and table.find(options, v) then
                map[v] = true
            end
            selectedByProfile[getProfileKey()] = { __multi = true, values = map }
        else
            if v ~= nil and table.find(options, v) then
                selectedByProfile[getProfileKey()] = v
            end
        end
        valLbl.Text = getDisplayText()
        rebuild()
    end

    function obj:Get()
        if isMulti then
            local entry = getOrInitProfileValue()
            local map   = entry.values or {}
            local out   = {}
            for _, opt in ipairs(options) do
                if map[opt] then
                    table.insert(out, opt)
                end
            end
            return out
        else
            return getOrInitProfileValue()
        end
    end

    function obj:_syncProfile()
        syncProfile()
    end

    obj._profileValues = selectedByProfile

    table.insert(self._controls, obj)
    return obj
end

-- controls: text (reiner Info-Text) ---------------------------------

function Section:CreateText(cfg)
    local win   = self.Window
    local inner = self.Inner
    local c     = cfg or {}

    local text    = c.Text or c.Value or "Text"
    local align   = (c.Align or c.Alignment or "left"):lower()
    local size    = c.Size or 11
    local wrapped = c.Wrap ~= false

    local xAlign = Enum.TextXAlignment.Left
    if align == "center" then
        xAlign = Enum.TextXAlignment.Center
    elseif align == "right" then
        xAlign = Enum.TextXAlignment.Right
    end

    local lbl = New("TextLabel", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 0, 0),
        AutomaticSize = Enum.AutomaticSize.Y,
        Font = Enum.Font.Gotham,
        Text = text,
        TextWrapped = wrapped,
        TextXAlignment = xAlign,
        TextYAlignment = Enum.TextYAlignment.Top,
        TextSize = size,
        Parent = inner,
    })
    win:_track(lbl, "TextColor3", "DimText")
    -- kein AutoFit

    local obj = {}
    function obj:SetText(t)
        lbl.Text = t or ""
    end
    function obj:GetText()
        return lbl.Text
    end

    table.insert(self._controls, obj)
    return obj
end

-- CreateWindow -------------------------------------------------------

function Vanith:CreateWindow(opts)
    opts = opts or {}

    local theme = {}
    for k, v in pairs(DEFAULT_THEME) do
        theme[k] = v
    end
    for k, v in pairs(opts.Theme or {}) do
        theme[k] = v
    end

    local parent = GetRootParent()

    local sizeOpt = opts.StartSize or opts.Size
    local initialSize
    if typeof(sizeOpt) == "Vector2" then
        initialSize = UDim2.fromOffset(sizeOpt.X, sizeOpt.Y)
    elseif typeof(sizeOpt) == "UDim2" then
        initialSize = sizeOpt
    else
        initialSize = UDim2.fromOffset(649, 417)
    end

    local baseWidth   = initialSize.X.Offset > 0 and initialSize.X.Offset or 649
    local baseHeight  = initialSize.Y.Offset > 0 and initialSize.Y.Offset or 417
    local minSizeVec  = Vector2.new(baseWidth, baseHeight)

    local isMobile = UserInputService.TouchEnabled
        and not UserInputService.KeyboardEnabled
        and not UserInputService.MouseEnabled

    local self = setmetatable({
        Theme              = theme,
        _themeBindings     = {},
        Tabs               = {},
        TopTabs            = {},
        MinSize            = minSizeVec,
        IsMobile           = isMobile,
        IsMinimized        = false,
        MiniButton         = nil,
        ConfigFolder       = opts.ConfigFolder or opts.ConfigFolderName or "VanithConfigs",
        ConfigExtension    = (opts.ConfigExtension or "json"):gsub("^%.+", ""),
        DefaultConfigName  = opts.ConfigName or opts.DefaultConfig or "default",
        ConfigName         = nil,
    }, Window)

    self.ConfigName = self.DefaultConfigName

    local gui = New("ScreenGui", {
        Name = opts.Name or "VanithUI",
        ResetOnSpawn = false,
        IgnoreGuiInset = true,
        Parent = parent,
    })
    self.Gui = gui

    local main = New("Frame", {
        Size = initialSize,
        Position = opts.Position or UDim2.fromScale(0.5, 0.5),
        AnchorPoint = Vector2.new(0.5, 0.5),
        BorderSizePixel = 0,
        Parent = gui,
    })
    self.Main = main
    self:_track(main, "BackgroundColor3", "BG0")
    New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = main})
    local ms = New("UIStroke", {Thickness = 1, Transparency = 0.2, Parent = main})
    self:_track(ms, "Color", "Stroke")
    -- clippt Theme-Deko innerhalb der UI
    main.ClipsDescendants = true

    local bgHolder = New("Frame", {
        Name = "BackgroundHolder",
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ClipsDescendants = true,
        Size = UDim2.fromScale(1, 1),
        ZIndex = 0,
        Parent = main,
    })
    self.BackgroundHolder = bgHolder

    local glass = New("Frame", {
        BackgroundColor3 = Color3.new(1, 1, 1),
        BackgroundTransparency = 0.97,
        BorderSizePixel = 0,
        Size = UDim2.fromScale(1, 1),
        Parent = main,
        ZIndex = 1,
    })
    New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = glass})

    local top = New("Frame", {
        BorderSizePixel = 0,
        Size = UDim2.new(1, 0, 0, 54),
        Parent = main,
        ZIndex = 2,
    })
    self.TopBar = top
    self:_track(top, "BackgroundColor3", "BG2")
    New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = top})

    local windowTitle = (type(opts.Title) == "string" and opts.Title) or "Vanith"
    self.Title = windowTitle

    local shortName = windowTitle
    local barIndex = string.find(windowTitle, "|")
    if barIndex then
        shortName = string.sub(windowTitle, 1, barIndex - 1)
        shortName = shortName:gsub("^%s+", ""):gsub("%s+$", "")
        if shortName == "" then
            shortName = windowTitle
        end
    end
    local miniLabelText = opts.MinimizeText or ("Open " .. shortName)

    -- brand box links
    local brand = New("Frame", {
        BorderSizePixel = 0,
        Position = UDim2.fromOffset(12, 10),
        Size = UDim2.fromOffset(140, 34),
        Parent = top,
        ZIndex = 3,
    })
    self:_track(brand, "BackgroundColor3", "BG2")
    New("UICorner", {CornerRadius = UDim.new(0, 8), Parent = brand})
    local bStroke = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = brand})
    self:_track(bStroke, "Color", "Stroke")

    local brandLabel = New("TextLabel", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, -8, 1, 0),
        Position = UDim2.new(0, 4, 0, 0),
        Font = Enum.Font.GothamBold,
        Text = windowTitle,
        TextSize = 14,
        TextXAlignment = Enum.TextXAlignment.Center,
        Parent = brand,
        ZIndex = 4,
    })
    self:_track(brandLabel, "TextColor3", "Accent")
    -- Fenster-Titel auto-fit, damit er nicht aus dem Rahmen geht
    AutoFitText(brandLabel, 14, 8, 8)

    -- middle strip mit top tabs
    local strip = New("Frame", {
        BorderSizePixel = 0,
        Position = UDim2.fromOffset(160, 10),
        Size = UDim2.new(1, -264, 0, 34),
        Parent = top,
        ZIndex = 2,
    })
    self:_track(strip, "BackgroundColor3", "BG2")
    New("UICorner", {CornerRadius = UDim.new(0, 8), Parent = strip})
    local sStroke = New("UIStroke", {Thickness = 1, Transparency = 0.5, Parent = strip})
    self:_track(sStroke, "Color", "Stroke")

    local tabsHolder = New("ScrollingFrame", {
        BackgroundTransparency = 1,
        Position = UDim2.fromOffset(8, 0),
        Size = UDim2.new(1, -16, 1, 0),
        Parent = strip,
        ZIndex = 3,
        CanvasSize = UDim2.new(0, 0, 0, 0),
        ScrollBarThickness = 0,
        ScrollBarImageTransparency = 0.3,
        ScrollBarImageColor3 = theme.Accent,
        ScrollingDirection = Enum.ScrollingDirection.X,
        ScrollingEnabled = false,
    })
    self.TopTabsHolder = tabsHolder

    local topLayout = New("UIListLayout", {
        FillDirection = Enum.FillDirection.Horizontal,
        SortOrder     = Enum.SortOrder.LayoutOrder,
        Padding       = UDim.new(0, 26),
        Parent        = tabsHolder,
    })
    topLayout.VerticalAlignment   = Enum.VerticalAlignment.Center
    topLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

    local function updateTopTabsLayout()
        local contentW = topLayout.AbsoluteContentSize.X
        local viewW    = tabsHolder.AbsoluteSize.X

        if contentW <= viewW or viewW == 0 then
            topLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
            tabsHolder.CanvasSize         = UDim2.new(0, viewW, 0, 0)
            tabsHolder.ScrollingEnabled   = false
            tabsHolder.ScrollBarThickness = 0
            tabsHolder.CanvasPosition     = Vector2.new(0, 0)
        else
            topLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
            tabsHolder.CanvasSize         = UDim2.new(0, contentW + 16, 0, 0)
            tabsHolder.ScrollingEnabled   = true
            tabsHolder.ScrollBarThickness = 2
        end
    end

    topLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(updateTopTabsLayout)
    tabsHolder:GetPropertyChangedSignal("AbsoluteSize"):Connect(updateTopTabsLayout)
    task.defer(updateTopTabsLayout)

    -- control box rechts (minimize / close)

    local controlBox = New("Frame", {
        BorderSizePixel = 0,
        Position = UDim2.new(1, -96, 0, 10),
        Size = UDim2.fromOffset(84, 34),
        Parent = top,
        ZIndex = 3,
    })
    self:_track(controlBox, "BackgroundColor3", "BG2")
    New("UICorner", {CornerRadius = UDim.new(0, 8), Parent = controlBox})
    local cbStroke = New("UIStroke", {Thickness = 1, Transparency = 0.55, Parent = controlBox})
    self:_track(cbStroke, "Color", "Stroke")

    local minimizeBtn = New("TextButton", {
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        Size = UDim2.new(0.5, 0, 1, 0),
        Position = UDim2.new(0, 0, 0, 0),
        Font = Enum.Font.GothamBold,
        Text = "–",
        TextSize = 16,
        Parent = controlBox,
        ZIndex = 4,
    })
    self:_track(minimizeBtn, "TextColor3", "Accent")

    local closeBtn = New("TextButton", {
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        Size = UDim2.new(0.5, 0, 1, 0),
        Position = UDim2.new(0.5, 0, 0, 0),
        Font = Enum.Font.GothamBold,
        Text = "X",
        TextSize = 16,
        Parent = controlBox,
        ZIndex = 4,
    })
    self:_track(closeBtn, "TextColor3", "Accent")

    minimizeBtn.MouseButton1Click:Connect(function()
        self:Minimize()
    end)

    closeBtn.MouseButton1Click:Connect(function()
        if self.Gui then
            self.Gui:Destroy()
        end
    end)

    minimizeBtn.MouseEnter:Connect(function()
        PlayTween(minimizeBtn, {TextColor3 = self.Theme.Text}, 0.08)
    end)
    minimizeBtn.MouseLeave:Connect(function()
        PlayTween(minimizeBtn, {TextColor3 = self.Theme.Accent}, 0.1)
    end)

    closeBtn.MouseEnter:Connect(function()
        PlayTween(closeBtn, {TextColor3 = Color3.fromRGB(255, 120, 120)}, 0.08)
    end)
    closeBtn.MouseLeave:Connect(function()
        PlayTween(closeBtn, {TextColor3 = self.Theme.Accent}, 0.1)
    end)

    -- sidebar / pages ------------------------------------------------

    local sidebar = New("Frame", {
        BorderSizePixel = 0,
        Position = UDim2.fromOffset(12, 66),
        Size = UDim2.new(0, 120, 1, -78),
        Parent = main,
        ZIndex = 1,
    })
    self.Sidebar = sidebar
    self:_track(sidebar, "BackgroundColor3", "BG2")
    New("UICorner", {CornerRadius = UDim.new(0, 8), Parent = sidebar})
    local sbStroke = New("UIStroke", {Thickness = 1, Transparency = 0.25, Parent = sidebar})
    self:_track(sbStroke, "Color", "Stroke")

    -- Tabs als ScrollFrame -> scrollbare Tab-Liste (auch Mobile)
    local sideList = New("ScrollingFrame", {
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        Position = UDim2.fromOffset(10, 14),
        Size = UDim2.new(1, -20, 1, -24),
        Parent = sidebar,
        ScrollBarThickness = 3,
        ScrollBarImageTransparency = 0.3,
        ScrollBarImageColor3 = theme.Accent,
        ScrollingDirection = Enum.ScrollingDirection.Y,
        CanvasSize = UDim2.new(0, 0, 0, 0),
        AutomaticCanvasSize = Enum.AutomaticSize.Y,
    })
    self.SideList = sideList

    local sideLayout = New("UIListLayout", {
        SortOrder = Enum.SortOrder.LayoutOrder,
        Padding   = UDim.new(0, 6),
        Parent    = sideList,
    })
    sideLayout.VerticalAlignment = Enum.VerticalAlignment.Top

    local pages = New("Frame", {
        BackgroundTransparency = 1,
        Position = UDim2.fromOffset(146, 66),
        Size = UDim2.new(1, -158, 1, -78),
        Parent = main,
    })
    self.Pages = pages

    local resize = New("Frame", {
        BackgroundTransparency = 1,
        Size = UDim2.fromOffset(16, 16),
        Position = UDim2.new(1, -18, 1, -18),
        Parent = main,
        ZIndex = 5,
    })
    local resizeLbl = New("TextLabel", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 1, 0),
        Font = Enum.Font.GothamBold,
        Text = "↘",
        TextSize = 14,
        Parent = resize,
    })
    self:_track(resizeLbl, "TextColor3", "Accent")

    self:_enableResize(resize, main)

    if opts.Draggable ~= false then
        self:_enableDrag(top, main)
    end

    local scale = New("UIScale", {Parent = main})
    local cam   = workspace.CurrentCamera

    if isMobile then
        scale.Scale = 0.70
    elseif cam then
        local vp = cam.ViewportSize
        if vp.X < 900 then
            scale.Scale = math.clamp(vp.X / 900, 0.75, 1)
        else
            scale.Scale = 1
        end
    else
        scale.Scale = 1
    end

    -- mini open button wenn minimiert --------------------------------

    local miniButton = New("Frame", {
        Name = "VanithMini",
        BackgroundTransparency = 0,
        Size = UDim2.fromOffset(190, 40),

        AnchorPoint = Vector2.new(0.5, 0),
        Position    = UDim2.new(0.5, 0, 0, 24),

        Parent  = gui,
        Visible = false,
        ZIndex  = 10,
    })

    self:_track(miniButton, "BackgroundColor3", "BG2")
    New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = miniButton})
    local miniStroke = New("UIStroke", {
        Thickness = 1.3,
        Transparency = 0.15,
        Parent = miniButton,
    })
    self:_track(miniStroke, "Color", "Stroke")

    local grip = New("Frame", {
        BorderSizePixel = 0,
        Size = UDim2.new(0, 40, 1, 0),
        Parent = miniButton,
        ZIndex = 11,
    })
    self:_track(grip, "BackgroundColor3", "BG1")
    New("UICorner", {CornerRadius = UDim.new(0, 10), Parent = grip})
    local gripStroke = New("UIStroke", {
        Thickness = 1,
        Transparency = 0.35,
        Parent = grip,
    })
    self:_track(gripStroke, "Color", "Stroke")

    local gripIcon = New("TextLabel", {
        BackgroundTransparency = 1,
        Size = UDim2.new(1, 0, 1, 0),
        Font = Enum.Font.GothamBold,
        Text = "▢",
        TextSize = 16,
        TextXAlignment = Enum.TextXAlignment.Center,
        TextYAlignment = Enum.TextYAlignment.Center,
        Parent = grip,
        ZIndex = 12,
    })
    self:_track(gripIcon, "TextColor3", "Muted")

    local miniLabel = New("TextLabel", {
        BackgroundTransparency = 1,
        Position = UDim2.new(0, 48, 0, 0),
        Size = UDim2.new(1, -58, 1, 0),
        Font = Enum.Font.Gotham,
        Text = miniLabelText,
        TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Left,
        TextYAlignment = Enum.TextYAlignment.Center,
        Parent = miniButton,
        ZIndex = 11,
    })
    self:_track(miniLabel, "TextColor3", "Text")

    local clickArea = New("TextButton", {
        AutoButtonColor = false,
        BackgroundTransparency = 1,
        Size = UDim2.new(1, -42, 1, 0),
        Position = UDim2.new(0, 42, 0, 0),
        Text = "",
        Parent = miniButton,
        ZIndex = 13,
    })

    local miniScale = New("UIScale", {Parent = miniButton})
    miniScale.Scale = scale.Scale

    self.MiniButton = miniButton

    self:_enableDrag(grip, miniButton)

    clickArea.MouseButton1Click:Connect(function()
        self:Restore()
    end)

    miniButton.MouseEnter:Connect(function()
        PlayTween(miniButton, {BackgroundColor3 = self.Theme.BG1}, 0.12)
    end)
    miniButton.MouseLeave:Connect(function()
        PlayTween(miniButton, {BackgroundColor3 = self.Theme.BG2}, 0.12)
    end)

    local autoLoad = opts.AutoLoadConfig or opts.AutoloadConfig
    if autoLoad then
        task.defer(function()
            if self.LoadConfig then
                self:LoadConfig(self.DefaultConfigName)
            end
        end)
    end

    -- optional direkt ein ThemePreset beim Erstellen anwenden
    local preset = opts.ThemePreset
    if type(preset) == "string" and preset ~= "" and self.SetThemePreset then
        self:SetThemePreset(preset)
    end

    return self
end

Vanith.Createwindow = Vanith.CreateWindow

-- config helpers -----------------------------------------------------

function Window:_ensureConfigFolder()
    local folder = self.ConfigFolder or "VanithConfigs"
    if isfolder and makefolder then
        local ok, exists = pcall(isfolder, folder)
        if ok and not exists then
            pcall(makefolder, folder)
        end
    end
    return folder
end

function Window:_getConfigPath(name)
    local cfgName = tostring(name or self.ConfigName or self.DefaultConfigName or "default")
    local folder  = self:_ensureConfigFolder()
    local ext     = (self.ConfigExtension or "json"):gsub("^%.+", "")
    return string.format("%s/%s.%s", folder, cfgName, ext)
end

function Window:ListConfigs()
    local folder = self:_ensureConfigFolder()
    local result = {}

    if listfiles and isfolder then
        local okExists, exists = pcall(isfolder, folder)
        if okExists and exists then
            local ok, files = pcall(listfiles, folder)
            if ok and type(files) == "table" then
                local ext = (self.ConfigExtension or "json"):gsub("^%.+", "")
                ext = ext:lower()

                for _, fullPath in ipairs(files) do
                    local isDir = false
                    if isfolder then
                        local okDir, dirResult = pcall(isfolder, fullPath)
                        isDir = okDir and dirResult or false
                    end
                    if not isDir then
                        local fname = fullPath:match("[^/\\]+$") or fullPath
                        local base, fileExt = fname:match("^(.*)%.([%w]+)$")
                        if fileExt and fileExt:lower() == ext then
                            table.insert(result, (base and base ~= "") and base or fname)
                        end
                    end
                end
            end
        end
    end

    table.sort(result)
    return result
end

-- Config serialisieren -----------------------------------------------

function Window:_buildConfigData()
    local data = {
        _meta = {
            title     = self.Title or "Vanith",
            game      = game.PlaceId,
            placeName = game.Name,
            savedAt   = os.time(),
            version   = 1,
        },
        tabs = {},
    }

    for ti, tab in ipairs(self.Tabs) do
        local tEntry = {
            name     = tab.Name,
            sections = {},
        }

        for si, sec in ipairs(tab._sections or {}) do
            local sEntry = {
                controls = {},
            }

            local controlIndex = 0

            for _, ctrl in ipairs(sec._controls or {}) do
                local pv = rawget(ctrl, "_profileValues")
                if type(pv) == "table" then
                    local profiles = {}

                    for key, val in pairs(pv) do
                        local keyName
                        if key == "__default" then
                            keyName = "__default"
                        elseif type(key) == "table" and key.Label and key.Label.Text then
                            keyName = key.Label.Text
                        else
                            keyName = tostring(key)
                        end

                        if type(val) == "boolean"
                        or type(val) == "number"
                        or type(val) == "string"
                        or type(val) == "table" then
                            profiles[keyName] = val
                        end
                    end

                    controlIndex = controlIndex + 1
                    sEntry.controls[controlIndex] = {
                        profiles = profiles,
                    }
                end
            end

            tEntry.sections[si] = sEntry
        end

        data.tabs[ti] = tEntry
    end

    return data
end

-- Config anwenden ----------------------------------------------------

function Window:_applyConfigData(data)
    if type(data) ~= "table" or type(data.tabs) ~= "table" then
        return
    end

    for ti, tab in ipairs(self.Tabs) do
        local tEntry = data.tabs[ti]
        if tEntry and type(tEntry.sections) == "table" then
            for si, sec in ipairs(tab._sections or {}) do
                local sEntry = tEntry.sections[si]
                if sEntry and type(sEntry.controls) == "table" then
                    local controlsData = sEntry.controls

                    -- alte Saves: string-Keys ("1","2",...)
                    local needsFix = false
                    for k, _ in pairs(controlsData) do
                        if type(k) ~= "number" then
                            needsFix = true
                            break
                        end
                    end

                    if needsFix then
                        local fixed = {}
                        for k, v in pairs(controlsData) do
                            local idx = tonumber(k)
                            if idx then
                                fixed[idx] = v
                            end
                        end
                        controlsData = fixed
                        sEntry.controls = fixed
                    end

                    local controlIndex = 0

                    for _, ctrl in ipairs(sec._controls or {}) do
                        if type(ctrl._profileValues) == "table" then
                            controlIndex = controlIndex + 1
                            local cEntry = controlsData[controlIndex]

                            if cEntry and type(cEntry.profiles) == "table" then
                                local pv = ctrl._profileValues

                                -- altes Table leeren, Referenzen behalten
                                for k in pairs(pv) do
                                    pv[k] = nil
                                end

                                for profName, val in pairs(cEntry.profiles) do
                                    local key
                                    if profName == "__default" then
                                        key = "__default"
                                    else
                                        for _, tt in ipairs(tab._topTabs or {}) do
                                            if tt.Label and tt.Label.Text == profName then
                                                key = tt
                                                break
                                            end
                                        end
                                    end

                                    if key then
                                        pv[key] = val
                                    end
                                end

                                if ctrl._syncProfile then
                                    ctrl:_syncProfile()
                                end
                            end
                        end
                    end
                end
            end
        end
    end
end

function Window:SaveConfig(name)
    local cfgName = name or self.ConfigName or self.DefaultConfigName or "default"
    self.ConfigName = cfgName

    if not writefile or not HttpService then
        return false, "filesystem or HttpService missing"
    end

    local ok, dataOrErr = pcall(function()
        return self:_buildConfigData()
    end)
    if not ok then
        return false, tostring(dataOrErr)
    end

    local path = self:_getConfigPath(cfgName)

    local okEncode, encoded = pcall(function()
        return HttpService:JSONEncode(dataOrErr)
    end)
    if not okEncode then
        return false, tostring(encoded)
    end

    local okWrite, errWrite = pcall(writefile, path, encoded)
    if not okWrite then
        return false, tostring(errWrite)
    end

    return true
end

function Window:LoadConfig(name)
    local cfgName = name or self.ConfigName or self.DefaultConfigName or "default"
    self.ConfigName = cfgName

    if not readfile or not HttpService then
        return false, "filesystem or HttpService missing"
    end

    local path = self:_getConfigPath(cfgName)
    local okRead, content = pcall(readfile, path)
    if not okRead then
        return false, "config '" .. cfgName .. "' could not be read"
    end

    local okDecode, data = pcall(function()
        return HttpService:JSONDecode(content)
    end)
    if not okDecode or type(data) ~= "table" then
        return false, "config '" .. cfgName .. "' is broken"
    end

    self:_applyConfigData(data)
    return true
end

function Window:DeleteConfig(name)
    local cfgName = name or self.ConfigName or self.DefaultConfigName or "default"

    if not delfile then
        return false, "delfile missing"
    end

    local path = self:_getConfigPath(cfgName)
    local ok, err = pcall(delfile, path)
    if not ok then
        return false, tostring(err)
    end

    if self.ConfigName == cfgName then
        self.ConfigName = nil
    end

    return true
end

-- Vanith:Config helper -----------------------------------------------

function Vanith:Config(cfg)
    cfg = cfg or {}
    local window  = cfg.Window
    local tab     = cfg.Tab
    local section = cfg.Section

    if not window then
        error("Vanith:Config -> cfg.Window is required")
    end

    if not section then
        if not tab then
            error("Vanith:Config -> cfg.Tab or cfg.Section is required")
        end
        section = tab:CreateSection(cfg.Title or "Configs", cfg.Slot or "RightBottom")
    end

    local function listConfigs()
        if window.ListConfigs then
            return window:ListConfigs()
        end
        return {}
    end

    local existing    = listConfigs()
    local defaultName = window.ConfigName or window.DefaultConfigName or existing[1] or "default"

    local nameBox = section:CreateTextbox({
        Default     = defaultName,
        Placeholder = "config name",
    })

    local dropdown = section:CreateDropdown({
        Name    = "Config list",
        Options = (#existing > 0) and existing or {defaultName},
        Default = defaultName,
    })

    local function refreshDropdown(selectName)
        local opts = listConfigs()
        if #opts == 0 then
            opts = {defaultName}
        end
        dropdown:SetOptions(opts)
        if selectName and table.find(opts, selectName) then
            dropdown:Set(selectName)
        end
    end

    section:CreateButton({
        Name = "Save",
        Callback = function()
            local name = nameBox:GetText()
            if not name or name == "" then
                window:Notify({
                    Title = "Config",
                    Text  = "Enter a config name first.",
                    Duration = 3,
                })
                return
            end

            local ok, err = window:SaveConfig(name)
            if not ok then
                window:Notify({
                    Title = "Config error",
                    Text  = tostring(err),
                    Duration = 4,
                })
                return
            end

            window:Notify({
                Title = "Config",
                Text  = "Saved '" .. name .. "'.",
                Duration = 3,
            })

            refreshDropdown(name)
        end
    })

    section:CreateButton({
        Name = "Load",
        Callback = function()
            local name = dropdown:Get()
            if not name or name == "" then
                window:Notify({
                    Title = "Config",
                    Text  = "Select a config first.",
                    Duration = 3,
                })
                return
            end

            local ok, err = window:LoadConfig(name)
            if not ok then
                window:Notify({
                    Title = "Config error",
                    Text  = tostring(err),
                    Duration = 4,
                })
                return
            end

            nameBox:SetText(name)

            window:Notify({
                Title = "Config",
                Text  = "Loaded '" .. name .. "'.",
                Duration = 3,
            })
        end
    })

    section:CreateButton({
        Name = "Delete",
        Callback = function()
            local name = dropdown:Get()
            if not name or name == "" then
                window:Notify({
                    Title = "Config",
                    Text  = "Select a config to delete.",
                    Duration = 3,
                })
                return
            end

            local ok, err = window:DeleteConfig(name)
            if not ok then
                window:Notify({
                    Title = "Config error",
                    Text  = tostring(err),
                    Duration = 4,
                })
                return
            end

            window:Notify({
                Title = "Config",
                Text  = "Deleted '" .. name .. "'.",
                Duration = 3,
            })

            refreshDropdown(nil)
        end
    })

    return {
        Section  = section,
        NameBox  = nameBox,
        Dropdown = dropdown,
    }
end

return Vanith
