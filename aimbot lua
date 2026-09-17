--[[
    ============================================================
    AIMBOT ASSIST — SINTA R P (v11)
    ============================================================
    - Mira baseada em ROTAÇÃO (funciona com o Sintonia reescrevendo CFrame)
    - Abas: Combat / Visual / Settings
    - Minimizar / Expandir + DRAG pelo header
    - Slider de Smoothness
    - WALL CHECK + DEATH CHECK
    ============================================================
--]]

local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace        = game:GetService("Workspace")
local TweenService     = game:GetService("TweenService")

local LocalPlayer = Players.LocalPlayer
local Camera      = Workspace.CurrentCamera

-- ============================================================
-- CONFIG
-- ============================================================
local CONFIG = {
    FOV_SIZE_DEFAULT    = 100,
    SMOOTHNESS_DEFAULT  = 50,
    AIM_KEY             = Enum.UserInputType.MouseButton2,
    WALL_CHECK_DEFAULT  = true,
    DEATH_CHECK_DEFAULT = true,
    FOV_COLOR           = Color3.fromRGB(79, 140, 255),
    PANEL_BG            = Color3.fromRGB(18, 22, 28),
    PANEL_BG_TRANS      = 0.15,
    TEXT_COLOR          = Color3.fromRGB(238, 242, 246),
    SUBTEXT_COLOR       = Color3.fromRGB(90, 100, 120),
    ACCENT              = Color3.fromRGB(79, 140, 255),
    PANEL_WIDTH         = 340,
    PANEL_HEIGHT        = 380,
    HEADER_HEIGHT       = 44,
    TABS_HEIGHT         = 40,
    ANIM_TIME           = 0.3,
}

-- ============================================================
-- UTILS
-- ============================================================
local Utils = {}

function Utils.create(className, props, children)
    local inst = Instance.new(className)
    for k, v in pairs(props or {}) do inst[k] = v end
    for _, child in ipairs(children or {}) do child.Parent = inst end
    return inst
end

function Utils.card(parent, height)
    local frame = Utils.create("Frame", {
        Size = UDim2.new(1, 0, 0, height),
        BackgroundColor3 = Color3.fromRGB(30, 34, 42),
        BackgroundTransparency = 0.4,
        BorderSizePixel = 0,
        Parent = parent,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(0, 14), Parent = frame })
    Utils.create("UIStroke", {
        Color = Color3.fromRGB(255, 255, 255),
        Transparency = 0.92, Thickness = 1, Parent = frame,
    })
    return frame
end

function Utils.label(parent, text, size, pos, color, bold)
    return Utils.create("TextLabel", {
        Size = size, Position = pos,
        BackgroundTransparency = 1, Text = text,
        TextColor3 = color or CONFIG.TEXT_COLOR,
        Font = bold and Enum.Font.GothamBold or Enum.Font.Gotham,
        TextSize = 14,
        TextXAlignment = Enum.TextXAlignment.Left,
        Parent = parent,
    })
end

function Utils.toggle(parent, defaultValue, callback)
    local state = defaultValue or false
    local holder = Utils.create("Frame", {
        Size = UDim2.new(0, 46, 0, 26),
        Position = UDim2.new(1, -46, 0.5, -13),
        BackgroundColor3 = state and CONFIG.ACCENT or Color3.fromRGB(42, 48, 64),
        BorderSizePixel = 0, Parent = parent,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = holder })

    local thumb = Utils.create("Frame", {
        Size = UDim2.new(0, 20, 0, 20),
        Position = state and UDim2.new(1, -23, 0.5, -10) or UDim2.new(0, 3, 0.5, -10),
        BackgroundColor3 = state and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(122, 132, 153),
        BorderSizePixel = 0, Parent = holder,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = thumb })

    local btn = Utils.create("TextButton", {
        Size = UDim2.new(1, 0, 1, 0),
        BackgroundTransparency = 1, Text = "", Parent = holder,
    })

    local function update()
        TweenService:Create(holder, TweenInfo.new(0.25), {
            BackgroundColor3 = state and CONFIG.ACCENT or Color3.fromRGB(42, 48, 64),
        }):Play()
        TweenService:Create(thumb, TweenInfo.new(0.25), {
            Position = state and UDim2.new(1, -23, 0.5, -10) or UDim2.new(0, 3, 0.5, -10),
            BackgroundColor3 = state and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(122, 132, 153),
        }):Play()
    end

    btn.MouseButton1Click:Connect(function()
        state = not state
        update()
        if callback then callback(state) end
    end)

    return { frame = holder, get = function() return state end, set = function(v) state = v; update() end }
end

function Utils.slider(parent, defaultPercent, callback)
    local percent = math.clamp(defaultPercent or 50, 0, 100)

    local track = Utils.create("Frame", {
        Size = UDim2.new(1, -28, 0, 6),
        Position = UDim2.new(0, 14, 0.5, 6),
        BackgroundColor3 = Color3.fromRGB(42, 48, 64),
        BorderSizePixel = 0, Parent = parent,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = track })

    local fill = Utils.create("Frame", {
        Size = UDim2.new(percent / 100, 0, 1, 0),
        BackgroundColor3 = CONFIG.ACCENT,
        BorderSizePixel = 0, Parent = track,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = fill })

    local thumb = Utils.create("Frame", {
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(percent / 100, 0, 0.5, 0),
        Size = UDim2.new(0, 16, 0, 16),
        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
        BorderSizePixel = 0, Parent = track,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = thumb })
    Utils.create("UIStroke", { Color = CONFIG.ACCENT, Thickness = 2, Parent = thumb })

    local valueLabel = Utils.create("TextLabel", {
        Size = UDim2.new(0, 40, 0, 16),
        Position = UDim2.new(1, -50, 0, -4),
        BackgroundTransparency = 1,
        Text = math.floor(percent) .. "%",
        TextColor3 = CONFIG.ACCENT,
        Font = Enum.Font.GothamBold, TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Right,
        Parent = parent,
    })

    local hitbox = Utils.create("TextButton", {
        Size = UDim2.new(1, -28, 0, 24),
        Position = UDim2.new(0, 14, 0.5, -6),
        BackgroundTransparency = 1, Text = "", Parent = parent,
    })

    local dragging = false

    local function setPercentFromX(mouseX)
        local absPos = track.AbsolutePosition
        local absSize = track.AbsoluteSize
        local rel = math.clamp((mouseX - absPos.X) / absSize.X, 0, 1)
        percent = math.floor(rel * 100)

        fill.Size = UDim2.new(rel, 0, 1, 0)
        thumb.Position = UDim2.new(rel, 0, 0.5, 0)
        valueLabel.Text = percent .. "%"

        if callback then callback(percent) end
    end

    hitbox.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            setPercentFromX(input.Position.X)
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch) then
            setPercentFromX(input.Position.X)
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)

    return {
        frame = track,
        get = function() return percent end,
        set = function(v)
            percent = math.clamp(v, 0, 100)
            fill.Size = UDim2.new(percent / 100, 0, 1, 0)
            thumb.Position = UDim2.new(percent / 100, 0, 0.5, 0)
            valueLabel.Text = math.floor(percent) .. "%"
            if callback then callback(percent) end
        end,
    }
end

function Utils.makeDraggable(handle, target)
    target = target or handle
    local dragging = false
    local dragStart, startPos

    handle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = target.Position
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if not dragging then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then
            local delta = input.Position - dragStart
            target.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
end

-- ============================================================
-- UI
-- ============================================================
local UI = {}
UI.__index = UI

local function makeTabButton(parent, text, order, onClick)
    local btn = Utils.create("TextButton", {
        Size = UDim2.new(0, 100, 1, 0),
        BackgroundColor3 = Color3.fromRGB(30, 34, 42),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        Text = text,
        TextColor3 = CONFIG.SUBTEXT_COLOR,
        Font = Enum.Font.GothamBold,
        TextSize = 13,
        LayoutOrder = order,
        AutoButtonColor = false,
        Parent = parent,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(0, 10), Parent = btn })

    local underline = Utils.create("Frame", {
        Name = "Underline",
        Size = UDim2.new(1, -16, 0, 2),
        Position = UDim2.new(0, 8, 1, -3),
        BackgroundColor3 = CONFIG.ACCENT,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        Parent = btn,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = underline })

    btn.MouseButton1Click:Connect(function()
        if onClick then onClick() end
    end)

    return {
        button = btn,
        underline = underline,
        setActive = function(active)
            TweenService:Create(btn, TweenInfo.new(0.2), {
                TextColor3 = active and CONFIG.TEXT_COLOR or CONFIG.SUBTEXT_COLOR,
                BackgroundTransparency = active and 0.85 or 1,
            }):Play()
            TweenService:Create(underline, TweenInfo.new(0.2), {
                BackgroundTransparency = active and 0 or 1,
            }):Play()
        end
    }
end

local function makePage(parent, order)
    local page = Utils.create("Frame", {
        Size = UDim2.new(1, 0, 1, 0),
        BackgroundTransparency = 1,
        Visible = false,
        LayoutOrder = order,
        Parent = parent,
    })
    Utils.create("UIListLayout", {
        Padding = UDim.new(0, 14),
        SortOrder = Enum.SortOrder.LayoutOrder,
        Parent = page,
    })
    Utils.create("UIPadding", {
        PaddingTop = UDim.new(0, 6), PaddingBottom = UDim.new(0, 12),
        PaddingLeft = UDim.new(0, 18), PaddingRight = UDim.new(0, 18),
        Parent = page,
    })
    return page
end

function UI.new()
    local self = setmetatable({}, UI)
    self.minimized = false
    self.currentTab = "Combat"

    self.gui = Utils.create("ScreenGui", {
        Name = "AimbotAssistUI",
        ResetOnSpawn = false,
        ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
        IgnoreGuiInset = true,
        Parent = LocalPlayer:WaitForChild("PlayerGui"),
    })

    local panel = Utils.create("Frame", {
        Name = "Panel",
        Size = UDim2.new(0, CONFIG.PANEL_WIDTH, 0, CONFIG.PANEL_HEIGHT),
        Position = UDim2.new(0, 30, 0.5, -CONFIG.PANEL_HEIGHT / 2),
        BackgroundColor3 = CONFIG.PANEL_BG,
        BackgroundTransparency = CONFIG.PANEL_BG_TRANS,
        BorderSizePixel = 0,
        ClipsDescendants = true,
        Parent = self.gui,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(0, 28), Parent = panel })
    Utils.create("UIStroke", {
        Color = Color3.fromRGB(255, 255, 255),
        Transparency = 0.92, Thickness = 1, Parent = panel,
    })
    Utils.create("UIGradient", {
        Rotation = 90,
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(40, 44, 54)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(18, 22, 28)),
        }),
        Transparency = NumberSequence.new({
            NumberSequenceKeypoint.new(0, 0.6),
            NumberSequenceKeypoint.new(1, 0.85),
        }),
        Parent = panel,
    })
    self.panel = panel

    -- HEADER
    local header = Utils.create("Frame", {
        Name = "Header",
        Size = UDim2.new(1, 0, 0, CONFIG.HEADER_HEIGHT),
        BackgroundTransparency = 1,
        Parent = panel,
    })

    Utils.label(header, "● Aimbot Assist",
        UDim2.new(1, -90, 1, 0), UDim2.new(0, 18, 0, 0),
        CONFIG.TEXT_COLOR, true).TextSize = 16

    local minimizeBtn = Utils.create("TextButton", {
        Name = "MinimizeBtn",
        Size = UDim2.new(0, 26, 0, 26),
        Position = UDim2.new(1, -66, 0.5, -13),
        BackgroundColor3 = Color3.fromRGB(40, 46, 58),
        BorderSizePixel = 0, Text = "—",
        TextColor3 = CONFIG.TEXT_COLOR,
        Font = Enum.Font.GothamBold, TextSize = 16,
        Parent = header,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(0, 8), Parent = minimizeBtn })
    Utils.create("UIStroke", {
        Color = Color3.fromRGB(255, 255, 255),
        Transparency = 0.9, Thickness = 1, Parent = minimizeBtn,
    })
    minimizeBtn.MouseEnter:Connect(function()
        TweenService:Create(minimizeBtn, TweenInfo.new(0.15), { BackgroundColor3 = CONFIG.ACCENT }):Play()
    end)
    minimizeBtn.MouseLeave:Connect(function()
        TweenService:Create(minimizeBtn, TweenInfo.new(0.15), { BackgroundColor3 = Color3.fromRGB(40, 46, 58) }):Play()
    end)

    local closeBtn = Utils.create("TextButton", {
        Name = "CloseBtn",
        Size = UDim2.new(0, 26, 0, 26),
        Position = UDim2.new(1, -38, 0.5, -13),
        BackgroundColor3 = Color3.fromRGB(40, 46, 58),
        BorderSizePixel = 0, Text = "✕",
        TextColor3 = CONFIG.TEXT_COLOR,
        Font = Enum.Font.GothamBold, TextSize = 14,
        Parent = header,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(0, 8), Parent = closeBtn })
    Utils.create("UIStroke", {
        Color = Color3.fromRGB(255, 255, 255),
        Transparency = 0.9, Thickness = 1, Parent = closeBtn,
    })
    closeBtn.MouseEnter:Connect(function()
        TweenService:Create(closeBtn, TweenInfo.new(0.15), { BackgroundColor3 = Color3.fromRGB(255, 79, 110) }):Play()
    end)
    closeBtn.MouseLeave:Connect(function()
        TweenService:Create(closeBtn, TweenInfo.new(0.15), { BackgroundColor3 = Color3.fromRGB(40, 46, 58) }):Play()
    end)

    -- TAB BAR
    local tabBar = Utils.create("Frame", {
        Name = "TabBar",
        Size = UDim2.new(1, 0, 0, CONFIG.TABS_HEIGHT),
        Position = UDim2.new(0, 0, 0, CONFIG.HEADER_HEIGHT),
        BackgroundColor3 = Color3.fromRGB(12, 15, 20),
        BackgroundTransparency = 0.4,
        BorderSizePixel = 0,
        Parent = panel,
    })
    Utils.create("UIListLayout", {
        FillDirection = Enum.FillDirection.Horizontal,
        HorizontalAlignment = Enum.HorizontalAlignment.Center,
        VerticalAlignment = Enum.VerticalAlignment.Center,
        Padding = UDim.new(0, 4),
        SortOrder = Enum.SortOrder.LayoutOrder,
        Parent = tabBar,
    })
    Utils.create("UIPadding", {
        PaddingLeft = UDim.new(0, 8),
        PaddingRight = UDim.new(0, 8),
        Parent = tabBar,
    })

    local pagesContainer = Utils.create("Frame", {
        Name = "Pages",
        Size = UDim2.new(1, 0, 1, -(CONFIG.HEADER_HEIGHT + CONFIG.TABS_HEIGHT)),
        Position = UDim2.new(0, 0, 0, CONFIG.HEADER_HEIGHT + CONFIG.TABS_HEIGHT),
        BackgroundTransparency = 1,
        Parent = panel,
    })
    self.pagesContainer = pagesContainer

    local pageCombat   = makePage(pagesContainer, 1)
    local pageVisual   = makePage(pagesContainer, 2)
    local pageSettings = makePage(pagesContainer, 3)

    self.pages = { Combat = pageCombat, Visual = pageVisual, Settings = pageSettings }

    local tabs = {}
    tabs.Combat   = makeTabButton(tabBar, "Combat",   1, function() self:switchTab("Combat") end)
    tabs.Visual   = makeTabButton(tabBar, "Visual",   2, function() self:switchTab("Visual") end)
    tabs.Settings = makeTabButton(tabBar, "Settings", 3, function() self:switchTab("Settings") end)
    self.tabs = tabs

    -- ===== COMBAT =====
    local aimbotCard = Utils.card(pageCombat, 50)
    aimbotCard.LayoutOrder = 1
    Utils.label(aimbotCard, "🎯  Aimbot", UDim2.new(0, 150, 1, 0), UDim2.new(0, 14, 0, 0), CONFIG.TEXT_COLOR, false)
    self.aimbotToggle = Utils.toggle(aimbotCard, false, function(v)
        if self.onAimbotChange then self.onAimbotChange(v) end
    end)

    local fovCard = Utils.card(pageCombat, 70)
    fovCard.LayoutOrder = 2
    Utils.label(fovCard, "⭕  FOV Size", UDim2.new(1, -30, 0, 18), UDim2.new(0, 14, 0, 8), CONFIG.TEXT_COLOR, false)

    local fovBox = Utils.create("TextBox", {
        Size = UDim2.new(1, -28, 0, 30),
        Position = UDim2.new(0, 14, 0, 32),
        BackgroundColor3 = Color3.fromRGB(10, 12, 16),
        BackgroundTransparency = 0.2, BorderSizePixel = 0,
        Text = tostring(CONFIG.FOV_SIZE_DEFAULT),
        TextColor3 = CONFIG.TEXT_COLOR,
        Font = Enum.Font.GothamMedium, TextSize = 14,
        TextXAlignment = Enum.TextXAlignment.Right,
        ClearTextOnFocus = false,
        Parent = fovCard,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(0, 10), Parent = fovBox })
    Utils.create("UIStroke", { Color = CONFIG.ACCENT, Transparency = 0.85, Thickness = 1, Parent = fovBox })

    fovBox:GetPropertyChangedSignal("Text"):Connect(function()
        local txt = fovBox.Text:gsub("%D", "")
        if txt ~= fovBox.Text then fovBox.Text = txt end
        local num = tonumber(txt)
        if num and num >= 20 and num <= 600 then
            if self.onFovSizeChange then self.onFovSizeChange(num) end
        end
    end)

    local smoothCard = Utils.card(pageCombat, 60)
    smoothCard.LayoutOrder = 3
    Utils.label(smoothCard, "🎚️  Smoothness", UDim2.new(1, -60, 0, 18), UDim2.new(0, 14, 0, 4), CONFIG.TEXT_COLOR, false)

    self.smoothSlider = Utils.slider(smoothCard, CONFIG.SMOOTHNESS_DEFAULT, function(v)
        if self.onSmoothnessChange then self.onSmoothnessChange(v) end
    end)

    -- ===== VISUAL =====
    local visualCard = Utils.card(pageVisual, 50)
    visualCard.LayoutOrder = 1
    Utils.label(visualCard, "🔵  FOV Circle", UDim2.new(0, 150, 1, 0), UDim2.new(0, 14, 0, 0), CONFIG.TEXT_COLOR, false)
    self.fovCircleToggle = Utils.toggle(visualCard, true, function(v)
        if self.onFovCircleChange then self.onFovCircleChange(v) end
    end)

    -- ===== SETTINGS =====
    local settingsCard = Utils.card(pageSettings, 50)
    settingsCard.LayoutOrder = 1
    Utils.label(settingsCard, "⚙️  Smooth Camera", UDim2.new(0, 180, 1, 0), UDim2.new(0, 14, 0, 0), CONFIG.TEXT_COLOR, false)
    self.smoothToggle = Utils.toggle(settingsCard, true, function(v)
        if self.onSmoothChange then self.onSmoothChange(v) end
    end)

    local wallCard = Utils.card(pageSettings, 50)
    wallCard.LayoutOrder = 2
    Utils.label(wallCard, "🧱  Wall Check", UDim2.new(0, 180, 1, 0), UDim2.new(0, 14, 0, 0), CONFIG.TEXT_COLOR, false)
    self.wallCheckToggle = Utils.toggle(wallCard, CONFIG.WALL_CHECK_DEFAULT, function(v)
        if self.onWallCheckChange then self.onWallCheckChange(v) end
    end)

    local deathCard = Utils.card(pageSettings, 50)
    deathCard.LayoutOrder = 3
    Utils.label(deathCard, "💀  Death Check", UDim2.new(0, 180, 1, 0), UDim2.new(0, 14, 0, 0), CONFIG.TEXT_COLOR, false)
    self.deathCheckToggle = Utils.toggle(deathCard, CONFIG.DEATH_CHECK_DEFAULT, function(v)
        if self.onDeathCheckChange then self.onDeathCheckChange(v) end
    end)

    minimizeBtn.MouseButton1Click:Connect(function() self:toggleMinimize() end)
    closeBtn.MouseButton1Click:Connect(function() self:close() end)

    Utils.makeDraggable(header, panel)
    self:switchTab("Combat")

    return self
end

function UI:switchTab(tabName)
    if self.currentTab == tabName then return end
    self.currentTab = tabName
    for name, page in pairs(self.pages) do
        local isActive = (name == tabName)
        page.Visible = isActive
        self.tabs[name].setActive(isActive)
    end
end

function UI:toggleMinimize()
    self.minimized = not self.minimized
    self:applyMinimizeState()
end

function UI:applyMinimizeState()
    local currentPos = self.panel.Position
    local centerY = currentPos.Y.Offset

    if self.minimized then
        centerY = centerY + (CONFIG.PANEL_HEIGHT - CONFIG.HEADER_HEIGHT) / 2
    else
        centerY = centerY - (CONFIG.PANEL_HEIGHT - CONFIG.HEADER_HEIGHT) / 2
    end

    local targetHeight = self.minimized and CONFIG.HEADER_HEIGHT or CONFIG.PANEL_HEIGHT

    self.panel.TabBar.Visible = not self.minimized
    self.pagesContainer.Visible = not self.minimized

    TweenService:Create(self.panel, TweenInfo.new(
        CONFIG.ANIM_TIME, Enum.EasingStyle.Quad, Enum.EasingDirection.Out
    ), {
        Size = UDim2.new(0, CONFIG.PANEL_WIDTH, 0, targetHeight),
        Position = UDim2.new(
            currentPos.X.Scale, currentPos.X.Offset,
            currentPos.Y.Scale, centerY
        ),
    }):Play()

    local minimizeBtn = self.panel.Header:FindFirstChild("MinimizeBtn")
    if minimizeBtn then
        minimizeBtn.Text = self.minimized and "▢" or "—"
    end
end

function UI:close()
    TweenService:Create(self.panel, TweenInfo.new(
        CONFIG.ANIM_TIME, Enum.EasingStyle.Quad, Enum.EasingDirection.Out
    ), {
        Position = UDim2.new(0, -400, 0.5, -CONFIG.PANEL_HEIGHT / 2),
        BackgroundTransparency = 1,
    }):Play()
    task.wait(CONFIG.ANIM_TIME)
    self.panel.Visible = false
end

-- ============================================================
-- FOV
-- ============================================================
local FOV = {}
FOV.__index = FOV

function FOV.new(screenGui)
    local self = setmetatable({}, FOV)
    self.size = CONFIG.FOV_SIZE_DEFAULT
    self.visible = true

    self.frame = Utils.create("Frame", {
        Name = "FOVCircle",
        AnchorPoint = Vector2.new(0.5, 0.5),
        Position = UDim2.new(0.5, 0, 0.5, 0),
        Size = UDim2.new(0, self.size, 0, self.size),
        BackgroundTransparency = 1, BorderSizePixel = 0,
        ZIndex = 5, Parent = screenGui,
    })
    Utils.create("UICorner", { CornerRadius = UDim.new(1, 0), Parent = self.frame })
    self.stroke = Utils.create("UIStroke", {
        Color = CONFIG.FOV_COLOR, Thickness = 2, Transparency = 0.15, Parent = self.frame,
    })
    return self
end

function FOV:setSize(size)
    self.size = size
    self.frame.Size = UDim2.new(0, size, 0, size)
end

function FOV:setVisible(visible)
    self.visible = visible
    self.frame.Visible = visible
end

-- ============================================================
-- AIMBOT (v11 — rotação baseada em desvio visual)
-- ============================================================
local Aimbot = {}
Aimbot.__index = Aimbot

function Aimbot.new()
    local self = setmetatable({}, Aimbot)
    self.enabled = false
    self.fovSize = CONFIG.FOV_SIZE_DEFAULT
    self.currentTarget = nil
    self.holdingAim = false
    self.smoothness = CONFIG.SMOOTHNESS_DEFAULT
    self.wallCheck = CONFIG.WALL_CHECK_DEFAULT
    self.deathCheck = CONFIG.DEATH_CHECK_DEFAULT

    self.raycastParams = RaycastParams.new()
    self.raycastParams.FilterType = Enum.RaycastFilterType.Exclude
    self.raycastParams.IgnoreWater = true
    self.raycastParams.FilterDescendantsInstances = { LocalPlayer.Character }

    LocalPlayer.CharacterAdded:Connect(function(newChar)
        self.raycastParams.FilterDescendantsInstances = { newChar }
    end)

    return self
end

local function getHeadScreenPos(character)
    if not character then return nil end
    local head = character:FindFirstChild("Head")
    if not head then return nil end
    local pos, onScreen = Camera:WorldToViewportPoint(head.Position)
    if not onScreen then return nil end
    return Vector2.new(pos.X, pos.Y)
end

function Aimbot:isValidTarget(player)
    if player == LocalPlayer then return false end
    local char = player.Character
    if not char then return false end

    if self.deathCheck then
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if not humanoid or humanoid.Health <= 0 then return false end
    end

    local head = char:FindFirstChild("Head")
    if not head then return false end

    local _, onScreen = Camera:WorldToViewportPoint(head.Position)
    if not onScreen then return false end

    if self.wallCheck then
        local myChar = LocalPlayer.Character
        if myChar then
            local myHead = myChar:FindFirstChild("Head")
            if myHead then
                local origin = myHead.Position
                local direction = (head.Position - origin)
                local result = Workspace:Raycast(origin, direction, self.raycastParams)
                if result and result.Instance then
                    if not result.Instance:IsDescendantOf(char) then
                        return false
                    end
                end
            end
        end
    end

    return true
end

function Aimbot:selectTarget()
    local best, bestDist = nil, math.huge
    local center = Camera.ViewportSize / 2
    local radius = self.fovSize / 2

    if self.currentTarget and self:isValidTarget(self.currentTarget) then
        local screenPos = getHeadScreenPos(self.currentTarget.Character)
        if screenPos then
            local d = (screenPos - center).Magnitude
            if d <= radius then
                return self.currentTarget
            end
        end
    end

    for _, player in ipairs(Players:GetPlayers()) do
        if self:isValidTarget(player) then
            local screenPos = getHeadScreenPos(player.Character)
            if screenPos then
                local d = (screenPos - center).Magnitude
                if d <= radius and d < bestDist then
                    best, bestDist = player, d
                end
            end
        end
    end
    return best
end

function Aimbot:getLerpFactor()
    local t = self.smoothness / 100
    return 1.0 - t * 0.95
end

function Aimbot:update()
    if not self.enabled or not self.holdingAim then
        self.currentTarget = nil
        return
    end

    local newTarget = self:selectTarget()
    self.currentTarget = newTarget
    if not self.currentTarget then return end

    local char = self.currentTarget.Character
    if not char then return end

    local targetHead = char:FindFirstChild("Head")
    if not targetHead then return end

    -- Rotação baseada no desvio visual
    local screenPos, onScreen = Camera:WorldToViewportPoint(targetHead.Position)
    if not onScreen then return end

    local viewportSize = Camera.ViewportSize
    local centerX = viewportSize.X / 2
    local centerY = viewportSize.Y / 2

    local dx = screenPos.X - centerX
    local dy = screenPos.Y - centerY

    if math.abs(dx) < 1.5 and math.abs(dy) < 1.5 then return end

    local fov = Camera.FieldOfView
    local yawRad   = math.atan(dx / centerX * math.tan(math.rad(fov / 2)))
    local pitchRad = math.atan(dy / centerY * math.tan(math.rad(fov / 2)))

    local currentCF = Camera.CFrame
    local newCF = currentCF * CFrame.Angles(-pitchRad, -yawRad, 0)

    local factor = self:getLerpFactor()
    Camera.CFrame = Camera.CFrame:Lerp(newCF, factor)
end

function Aimbot:setEnabled(v)
    self.enabled = v
    if not v then self.currentTarget = nil end
end

function Aimbot:setFovSize(v)      self.fovSize = v end
function Aimbot:setSmoothness(v)   self.smoothness = v end
function Aimbot:setWallCheck(v)    self.wallCheck = v end
function Aimbot:setDeathCheck(v)   self.deathCheck = v end

-- ============================================================
-- MAIN
-- ============================================================
local ui  = UI.new()
local fov = FOV.new(ui.gui)
local aim = Aimbot.new()

ui.onAimbotChange = function(enabled)
    aim:setEnabled(enabled)
    fov:setVisible(enabled and ui.fovCircleToggle:get())
end

ui.onFovSizeChange = function(size)
    aim:setFovSize(size)
    fov:setSize(size)
end

ui.onFovCircleChange = function(visible)
    fov:setVisible(visible and ui.aimbotToggle:get())
end

ui.onSmoothnessChange = function(v) aim:setSmoothness(v) end
ui.onWallCheckChange  = function(v) aim:setWallCheck(v) end
ui.onDeathCheckChange = function(v) aim:setDeathCheck(v) end

UserInputService.InputBegan:Connect(function(input, processed)
    if input.UserInputType == CONFIG.AIM_KEY then
        aim.holdingAim = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == CONFIG.AIM_KEY then
        aim.holdingAim = false
    end
end)

RunService:BindToRenderStep("AimbotUpdate_v11", Enum.RenderPriority.Last.Value, function()
    aim:update()
end)
