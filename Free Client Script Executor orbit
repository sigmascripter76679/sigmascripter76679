-- SERVER SCRIPT (Place in ServerScriptService)

local rs = game:GetService("RunService")
local players = game:GetService("Players")

-- Function to start orbit for a specific player
local function startOrbit(player)
	local character = player.Character
	if not character then return end
	
	local hrp = character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	
	-- Move all tools from backpack to character
	for _, v in pairs(player.Backpack:GetChildren()) do
		v.Parent = character
	end
	
	-- Collect tool handles
	local tools = {}
	for _, v in pairs(character:GetChildren()) do
		if v:IsA("Tool") then
			table.insert(tools, v.Handle)
		end
	end
	
	if #tools == 0 then return end
	
	-- Destroy RightHand
	local rightHand = character:FindFirstChild("RightHand")
	if rightHand then
		rightHand:Destroy()
	end
	
	-- Orbit parameters
	local speed = 1
	local angle = 0
	local r = 5
	local hrppos = hrp.Position
	local numtools = #tools
	
	-- Initial positioning
	for i, v in pairs(tools) do
		local equalangle = (i / numtools) * 2 * math.pi
		local x = hrppos.X + math.sin(equalangle) * r
		local y = hrppos.Y
		local z = hrppos.Z + math.cos(equalangle) * r
		v.CFrame = CFrame.new(Vector3.new(x, y, z))
	end
	
	-- Create orbit connection
	local orbitconnection
	orbitconnection = rs.Heartbeat:Connect(function(dt)
		if not character.Parent or not hrp.Parent then
			if orbitconnection then
				orbitconnection:Disconnect()
			end
			return
		end
		
		angle = angle + speed * dt
		local pos = hrp.Position
		
		for i, v in pairs(tools) do
			if v and v.Parent then
				local toolangle = angle + (i / numtools) * 2 * math.pi
				local x = pos.X + math.sin(toolangle) * r
				local y = pos.Y + math.sin(time() * 3) * 1
				local z = pos.Z + math.cos(toolangle) * r
				v.CFrame = CFrame.new(Vector3.new(x, y, z))
			end
		end
	end)
	
	-- Store connection on player for later access
	player:SetAttribute("OrbitConnection", true)
	
	-- Chat listener for stop command
	local conn
	conn = player.Chatted:Connect(function(msg)
		if msg == ";unmtorbit" then
			conn:Disconnect()
			conn = nil
			if orbitconnection then
				orbitconnection:Disconnect()
				orbitconnection = nil
			end
			player:SetAttribute("OrbitConnection", false)
		end
	end)
	
	-- Cleanup on character removal
	character.AncestryChanged:Connect(function()
		if not character.Parent then
			if conn then
				conn:Disconnect()
			end
			if orbitconnection then
				orbitconnection:Disconnect()
			end
		end
	end)
end

-- Auto-start orbit when player spawns (remove this if you want manual activation)
players.PlayerAdded:Connect(function(player)
	player.CharacterAdded:Connect(function(character)
		character:WaitForChild("HumanoidRootPart")
		wait(0.5) -- Wait for tools to load
		startOrbit(player)
	end)
end)

-- Handle existing players
for _, player in pairs(players:GetPlayers()) do
	if player.Character then
		startOrbit(player)
	end
	player.CharacterAdded:Connect(function(character)
		character:WaitForChild("HumanoidRootPart")
		wait(0.5)
		startOrbit(player)
	end)
end
