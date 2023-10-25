--MADE BY BLUELOOPS9
--*Cough cough* All rights reserved.
-- Made in 2023.

local Modem = GetPartFromPort(1, "Modem")
local Sign = GetPartFromPort(1, "Sign")
local Keyboard = GetPartFromPort(1, "Keyboard")

KeyboardOutput = ""
KeyboardWait = true
local function Text(Text)
	Sign:Configure({SignText=Text})
end
local function Wait()
	KeyboardWait = true
	repeat wait(0.1) until KeyboardWait == false
	return KeyboardOutput
end

Keyboard:Connect("TextInputted", function(Text)
	KeyboardOutput = Text:sub(1,#Text-1)
	KeyboardWait = false
end)
local function Loop()
	Text("Press enter to activate.")
	Enter = Wait()
	Text("Would you like to Signup, do a Transaction or Check balance?")
	Option = Wait():lower()
	if Option == "signup" then
		Text("Enter your username.")
		Username = Wait()
		Text("Enter your wanted pass.")
		Password = Wait()
		Text("Brodcasting...")
		Content = JSONEncode({Method="Signup",Username=Username,Password=Password})
		local Data, Success = Modem:RealPostRequest("https://darkbluestealth.pythonanywhere.com", Content, false, buh, {["Content-Type"]="application/json"})
		if Data == "200" then
			Text("Account has been created.")
		end
		if Data == "400" then
			Text("Account already exists!")
		end
		wait(3)
		Loop()
	end
	if Option == "transaction" then
		Text("Enter your username.")
		Username = Wait()
		Text("Enter your pass.")
		Password = Wait()
		Text("Enter recipent username.")
		Receiver = Wait()
		Text("Enter amount.")
		Amount = Wait()
		Text("Brodcasting...")
		Content = JSONEncode({Method="Transaction",Username=Username,Password=Password,ReceivingUser=Receiver,Amount=Amount})
		local Data, Success = Modem:RealPostRequest("https://darkbluestealth.pythonanywhere.com", Content, false, buh, {["Content-Type"]="application/json"})
		if Data == "404" then
			Text("Invalid pass.")
		end
		if Data == "400" then
			Text("Invalid username.")
		end
		if Data == "407" then
			Text("Invalid recipent.")
		end
		if Data == "402" then
			Text("You cant take BlueCoin, silly.")
		end
		if Data == "401" then
			Text("You dont have enough BlueCoin!")
		end
		if Data == "200" then
			Text("Transaction has been successful.")
		end
		wait(3)
		Loop()
	end
	if Option == "check balance" then
		Text("Enter your username.")
		Username = Wait()
		Text("Enter your pass.")
		Password = Wait()
		Text("Brodcasting...")
		Content = JSONEncode({Method="CheckBalance",Username=Username,Password=Password})
		local Data, Success = Modem:RealPostRequest("https://darkbluestealth.pythonanywhere.com", Content, false, buh, {["Content-Type"]="application/json"})
		Errored = false
		if Data == "E407" then
			Text("Invalid username.")
			wait(3)
			Loop()
		end
		if Data == "E406" then
			Text("Invalid pass.")
			wait(3)
			Loop()
		end
		if Data ~= "E406" and Data ~= "E407" then
			Text("Your balance is "..Data)
			wait(3)
			Loop()
		end
	end
	if Option ~= "check balance" and Option ~= "signup" and Option ~= "transaction" then
		Loop()
	end
end
Loop()
