# 🎯 TaskMaster - Decentralized Task Management

A powerful, gamified task management application built with AO (Arweave Operating System) and Nuxt.js. TaskMaster demonstrates the ease and power of building decentralized applications with AO, Lua, and Arweave.

## ✨ Features

### 🎮 Gamified Task Management
- **Create Tasks** - Add tasks with title, description, and priority levels
- **Complete Tasks** - Mark tasks as done and earn points
- **Points System** - Earn +10 points for creating tasks, +25 for completing them
- **Leaderboard** - Compete with other users on the global leaderboard
- **Task Priority** - Organize tasks by high, medium, or low priority
- **Tabbed Interface** - Clean navigation between Home and Leaderboard
- **Responsive Design** - Mobile-optimized components for all devices

### 🔗 AO Integration
- **Decentralized Storage** - All data stored permanently on Arweave
- **AO Processes** - Smart contract logic written in Lua
- **Wallet Integration** - Connect with ArConnect or other Arweave wallets
- **Real-time Updates** - Instant task creation and completion
- **Modern AO Patterns** - Using `Handlers.utils.hasMatchingTag` for message handling
- **Response Parsing** - Proper message handling and error management

### 🎨 Modern UI/UX
- **Dark Theme** - Modern glassmorphism design with dark aesthetics
- **Interactive Elements** - Dropdown menus, loading states, and smooth animations
- **Wallet Management** - Interactive wallet dropdown with copy and disconnect options
- **Tabbed Navigation** - Clean interface with Home and Leaderboard tabs
- **Mobile-First Design** - Responsive layout optimized for all screen sizes

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ and npm/yarn/pnpm
- ArConnect wallet extension (or compatible Arweave wallet)
- aos CLI for AO process deployment

### 1. Install Dependencies
\`\`\`bash
npm install
# or
yarn install
# or
pnpm install
\`\`\`

### 2. Deploy the AO Process
\`\`\`bash
# Start aos CLI
aos taskmaster

# Load the TaskMaster process
.load ao/task-process.lua

# Copy the Process ID - you'll need it for the frontend
\`\`\`

### 3. Start Development Server
\`\`\`bash
npm run dev
# or
yarn dev
# or
pnpm dev
\`\`\`

### 4. Connect and Use
1. Open your browser to the development server (usually http://localhost:3000)
2. Click "Connect Arweave Wallet" and approve the connection
3. Enter your AO Process ID from step 2
4. Start creating and managing your tasks!

## 🏗️ Project Structure

\`\`\`
├── app/                       # Nuxt.js App Directory
│   ├── app.vue               # Main app layout
│   ├── components/           # Vue components
│   │   ├── WalletConnect.vue # Wallet connection with dropdown
│   │   ├── TaskList.vue      # Task list with table layout
│   │   ├── CreateTask.vue    # Task creation form
│   │   ├── PointsDisplay.vue # Points display
│   │   ├── Leaderboard.vue  # Leaderboard with rankings
│   │   └── Navbar.vue        # Responsive navigation
│   └── lib/                  # Utility libraries
│       └── aoconnect.ts      # AO client library
├── ao/                       # AO process files
│   └── task-process.lua      # Main TaskMaster AO process
├── public/                   # Static assets
├── package.json              # Dependencies and scripts
└── README.md                 # This file
\`\`\`

## 🔧 AO Process API

The TaskMaster AO process supports the following actions:

### CreateTask
Creates a new task and awards 10 points.
\`\`\`json
{
  "title": "Task Title",
  "description": "Task description",
  "priority": "high|medium|low"
}
\`\`\`

### CompleteTask
Marks a task as completed and awards 25 points.
\`\`\`
Tags: {"TaskId": "task-id"}
\`\`\`

### GetTasks
Retrieves all tasks for the current user.

### GetPoints
Gets the current user's point balance.

### GetLeaderboard
Retrieves the top 10 users by points.

### DeleteTask
Deletes a task (only by the task owner).
\`\`\`
Tags: {"TaskId": "task-id"}
\`\`\`

## 🎯 Learning AO Development

This project demonstrates key AO development concepts:

### 1. **AO Processes**
- Lua-based smart contracts
- Message handling with Handlers.add()
- State management with local variables
- Cross-process communication with ao.send()

### 2. **aoconnect Integration**
- JavaScript/TypeScript client library
- Wallet integration with createSigner()
- Message sending with proper tags and data
- Error handling and response parsing
- Modern messaging patterns with `ao.send().receive()`

### 3. **Arweave Integration**
- Permanent data storage
- Wallet-based authentication
- Transaction signing and submission

### 4. **Decentralized Architecture**
- No central server required
- Data ownership by users
- Censorship-resistant storage
- Global accessibility

## 🛠️ Development

### Adding New Features
1. **AO Process**: Add new handlers in \`ao/task-process.lua\`
2. **Frontend**: Update components and AO client in \`app/lib/aoconnect.ts\`
3. **UI**: Modify Vue components in \`app/components/\`

### Testing
\`\`\`bash
# Test AO process
aos taskmaster
.load ao/task-process.lua
.spawn

# Test individual actions
Send({Target = ao.id, Action = "CreateTask", Data = '{"title": "Test", "priority": "high"}'})
Send({Target = ao.id, Action = "GetTasks", Data = "get"})
\`\`\`

### Deployment
\`\`\`bash
# Build the project
npm run build

# Deploy to production
npm run deploy
\`\`\`

## 🔗 Resources

- [AO Documentation](https://cookbook_ao.arweave.net/)
- [aoconnect Guide](https://cookbook_ao.arweave.net/guides/aoconnect/)
- [Arweave Documentation](https://docs.arweave.org/)
- [Forge CLI](https://github.com/your-org/ao-forge) - AO development toolkit

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is open source and available under the MIT License.

---

**Built with ❤️ using AO, Arweave, and modern web frameworks**

*TaskMaster demonstrates the power and simplicity of decentralized application development with AO.*