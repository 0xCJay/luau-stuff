local confgs = {
    ["BoxColor"] = Color3.fromRGB(255, 255, 255),
    ["OutlineColor"] = Color3.fromRGB(255, 255, 255),
    ["teamcheck"] = false
}

local runservice = game:GetService("RunService")
local cam = workspace.CurrentCamera
local espstuff = {}

local vec2 = Vector2.new
local drawthing = Drawing.new
local function wtvp(position)
    local screenpos, vis = cam:WorldToViewportPoint(position)
    return vec2(screenpos.X, screenpos.Y), vis, screenpos.Z
end

local function makeesp(model)
    if espstuff[model] then return end

    local boxes = {}
    boxes.bx = drawthing("Square")
    boxes.bx.Thickness = 1
    boxes.bx.Filled = false
    boxes.bx.Color = confgs["BoxColor"]
    boxes.bx.Visible = false
    boxes.bx.ZIndex = 2

    boxes.bxoutline = drawthing("Square")
    boxes.bxoutline.Thickness = 3
    boxes.bxoutline.Filled = false
    boxes.bxoutline.Color = confgs["OutlineColor"]
    boxes.bxoutline.Visible = false
    boxes.bxoutline.ZIndex = 1

    espstuff[model] = boxes
end

local function removeesp(model)
    if espstuff[model] then
        for _, d in pairs(espstuff[model]) do
            d:Remove()
        end
        espstuff[model] = nil
    end
end

local function updateesp(model, boxes)
    local primary = model.PrimaryPart or model:FindFirstChildWhichIsA("BasePart")
    if not primary then
        removeesp(model)
        return
    end

    local size = model:GetExtentsSize()
    local cf = primary.CFrame
    local corners = {
        (cf * CFrame.new(size.X / 2, size.Y / 2, size.Z / 2)).Position,
        (cf * CFrame.new(-size.X / 2, size.Y / 2, size.Z / 2)).Position,
        (cf * CFrame.new(size.X / 2, -size.Y / 2, size.Z / 2)).Position,
        (cf * CFrame.new(-size.X / 2, -size.Y / 2, size.Z / 2)).Position
    }

    local minx, miny, maxx, maxy = math.huge, math.huge, -math.huge, -math.huge
    local vis = false

    for _, c in ipairs(corners) do
        local sp, v = wtvp(c)
        if v then
            vis = true
            minx = math.min(minx, sp.X)
            miny = math.min(miny, sp.Y)
            maxx = math.max(maxx, sp.X)
            maxy = math.max(maxy, sp.Y)
        end
    end

    if not vis then
        boxes.bx.Visible = false
        boxes.bxoutline.Visible = false
        return
    end

    local bxsize = vec2(maxx - minx, maxy - miny)
    local bxpos = vec2(minx, miny)
    boxes.bx.Size = bxsize
    boxes.bx.Position = bxpos
    boxes.bx.Visible = true
    boxes.bxoutline.Size = bxsize
    boxes.bxoutline.Position = bxpos
    boxes.bxoutline.Visible = true
end

local function updateall()
    for _, model in pairs(workspace:GetChildren()) do
        if not (model and model:IsA("Model") and model.Name == "soldier_model") then
            removeesp(model)
        elseif model:FindFirstChild("friendly_marker") and confgs["teamcheck"] then
            removeesp(model)
        else
            if not espstuff[model] then makeesp(model) end
            local boxes = espstuff[model]
            if boxes then updateesp(model, boxes) end
        end
    end
end

game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        player.CharacterRemoving:Connect(function()
            if espstuff[character] then
                removeesp(character)
            end
        end)
    end)
end)

runservice.Heartbeat:Connect(function()
    updateall()
end)
