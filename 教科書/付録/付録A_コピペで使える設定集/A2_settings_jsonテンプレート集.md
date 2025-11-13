# A2. settings.jsonテンプレート集

## settings.jsonの配置場所

```bash
# プロジェクト固有の設定
<project-root>/.claude/settings.json

# ユーザー全体の設定
~/.claude/settings.json

# WSL環境の場合
\\wsl$\Ubuntu\home\username\.claude\settings.json
```

---

## 基本設定テンプレート

### 最小構成

```json
{
  "permissions": {
    "read": ["**/*"],
    "write": ["**/*"],
    "execute": ["npm", "git"]
  },
  "maxTokensPerRequest": 4096,
  "defaultModel": "claude-sonnet-4"
}
```

---

## パーミッション設定パターン

### 1. 安全重視設定（推奨）

```json
{
  "permissions": {
    "read": [
      "src/**/*",
      "public/**/*",
      "tests/**/*",
      "*.md",
      "package.json",
      "tsconfig.json"
    ],
    "write": [
      "src/**/*",
      "tests/**/*",
      "docs/**/*"
    ],
    "execute": [
      "npm run test",
      "npm run build",
      "npm run lint",
      "git status",
      "git diff",
      "git add",
      "git commit"
    ],
    "deny": {
      "read": [
        ".env",
        ".env.local",
        "*.key",
        "*.pem",
        "node_modules/**",
        ".git/**"
      ],
      "write": [
        ".env",
        ".env.local",
        "package.json",
        "package-lock.json"
      ],
      "execute": [
        "rm -rf",
        "git push --force",
        "npm publish"
      ]
    }
  }
}
```

### 2. 開発効率重視設定

```json
{
  "permissions": {
    "read": ["**/*"],
    "write": [
      "src/**/*",
      "tests/**/*",
      "docs/**/*",
      "public/**/*",
      "scripts/**/*"
    ],
    "execute": [
      "npm",
      "yarn",
      "pnpm",
      "git",
      "docker",
      "curl",
      "wget"
    ],
    "deny": {
      "read": [
        ".env",
        "*.key",
        "*.pem",
        "credentials.json"
      ],
      "write": [
        ".env"
      ],
      "execute": [
        "rm -rf /",
        "git push --force origin main"
      ]
    }
  }
}
```

### 3. 読み取り専用設定（コードレビュー用）

```json
{
  "permissions": {
    "read": ["**/*"],
    "write": [],
    "execute": [
      "git status",
      "git log",
      "git diff",
      "npm run test"
    ]
  },
  "autoApprove": false
}
```

---

## プロジェクトタイプ別設定

### フロントエンドプロジェクト

```json
{
  "permissions": {
    "read": [
      "src/**/*",
      "public/**/*",
      "components/**/*",
      "pages/**/*",
      "styles/**/*",
      "tests/**/*",
      "*.config.js",
      "*.config.ts",
      "package.json",
      "tsconfig.json"
    ],
    "write": [
      "src/**/*",
      "components/**/*",
      "pages/**/*",
      "styles/**/*",
      "tests/**/*",
      "public/**/*"
    ],
    "execute": [
      "npm run dev",
      "npm run build",
      "npm run test",
      "npm run lint",
      "npm run format",
      "git status",
      "git diff",
      "git add",
      "git commit"
    ],
    "deny": {
      "read": [".env.local", ".env.production"],
      "write": ["package.json", "package-lock.json"],
      "execute": ["npm install", "npm publish"]
    }
  },
  "ignoreDirs": [
    "node_modules",
    ".next",
    "dist",
    "build",
    ".cache"
  ],
  "maxTokensPerRequest": 8192
}
```

### バックエンドプロジェクト

```json
{
  "permissions": {
    "read": [
      "src/**/*",
      "controllers/**/*",
      "services/**/*",
      "models/**/*",
      "routes/**/*",
      "middlewares/**/*",
      "tests/**/*",
      "prisma/**/*",
      "*.config.js",
      "package.json"
    ],
    "write": [
      "src/**/*",
      "controllers/**/*",
      "services/**/*",
      "models/**/*",
      "routes/**/*",
      "middlewares/**/*",
      "tests/**/*",
      "prisma/schema.prisma"
    ],
    "execute": [
      "npm run dev",
      "npm run build",
      "npm run test",
      "npm run lint",
      "npx prisma migrate dev",
      "npx prisma generate",
      "docker-compose up -d",
      "docker-compose down",
      "git status",
      "git diff"
    ],
    "deny": {
      "read": [
        ".env",
        ".env.local",
        ".env.production",
        "*.key",
        "*.pem"
      ],
      "write": [".env"],
      "execute": [
        "npm publish",
        "docker system prune -a",
        "dropdb"
      ]
    }
  },
  "ignoreDirs": [
    "node_modules",
    "dist",
    "build",
    "coverage"
  ]
}
```

### フルスタックプロジェクト（Next.js）

```json
{
  "permissions": {
    "read": [
      "app/**/*",
      "pages/**/*",
      "components/**/*",
      "lib/**/*",
      "prisma/**/*",
      "public/**/*",
      "tests/**/*",
      "*.config.*",
      "package.json",
      "tsconfig.json"
    ],
    "write": [
      "app/**/*",
      "pages/**/*",
      "components/**/*",
      "lib/**/*",
      "prisma/schema.prisma",
      "public/**/*",
      "tests/**/*"
    ],
    "execute": [
      "npm run dev",
      "npm run build",
      "npm run start",
      "npm run test",
      "npm run lint",
      "npx prisma migrate dev",
      "npx prisma studio",
      "git status",
      "git diff",
      "git add",
      "git commit"
    ],
    "deny": {
      "read": [".env.local"],
      "write": [".env.local", "package.json"],
      "execute": ["npm publish", "vercel --prod"]
    }
  },
  "ignoreDirs": [
    "node_modules",
    ".next",
    "dist",
    "coverage"
  ],
  "maxTokensPerRequest": 8192
}
```

---

## セキュリティ重視設定

### 最高セキュリティレベル

```json
{
  "permissions": {
    "read": [
      "src/**/*.{ts,tsx,js,jsx}",
      "tests/**/*.{ts,tsx,js,jsx}",
      "README.md",
      "package.json"
    ],
    "write": [
      "src/**/*.{ts,tsx,js,jsx}",
      "tests/**/*.{ts,tsx,js,jsx}",
      "docs/**/*.md"
    ],
    "execute": [
      "npm run test",
      "npm run lint",
      "git status",
      "git diff"
    ],
    "deny": {
      "read": [
        ".env*",
        "*.key",
        "*.pem",
        "*.p12",
        "*.pfx",
        "credentials.*",
        "secrets.*",
        ".aws/**",
        ".ssh/**",
        "node_modules/**",
        ".git/**"
      ],
      "write": [
        ".env*",
        "package.json",
        "package-lock.json",
        "yarn.lock",
        "pnpm-lock.yaml",
        "*.config.js",
        "*.config.ts"
      ],
      "execute": [
        "rm",
        "mv",
        "cp",
        "chmod",
        "chown",
        "sudo",
        "curl",
        "wget",
        "npm install",
        "npm uninstall",
        "npm publish",
        "git push",
        "git reset --hard",
        "git clean -fd",
        "docker",
        "kubectl"
      ]
    }
  },
  "autoApprove": false,
  "requireConfirmation": true,
  "maxTokensPerRequest": 4096,
  "rateLimiting": {
    "enabled": true,
    "maxRequestsPerMinute": 10
  }
}
```

### 本番環境デプロイ用

```json
{
  "permissions": {
    "read": [
      "src/**/*",
      "dist/**/*",
      "build/**/*",
      "package.json",
      "Dockerfile",
      "docker-compose.yml"
    ],
    "write": [],
    "execute": [
      "npm run build",
      "npm run test",
      "docker build",
      "docker-compose up",
      "git status",
      "git tag"
    ],
    "deny": {
      "read": [".env.production"],
      "write": ["**/*"],
      "execute": [
        "git push",
        "docker push",
        "kubectl apply",
        "aws",
        "gcloud"
      ]
    }
  },
  "autoApprove": false,
  "requireConfirmation": true
}
```

---

## 特殊用途設定

### データ分析・機械学習プロジェクト

```json
{
  "permissions": {
    "read": [
      "notebooks/**/*.ipynb",
      "src/**/*.py",
      "data/**/*.csv",
      "data/**/*.json",
      "models/**/*",
      "tests/**/*"
    ],
    "write": [
      "notebooks/**/*.ipynb",
      "src/**/*.py",
      "outputs/**/*",
      "results/**/*",
      "tests/**/*"
    ],
    "execute": [
      "python",
      "pip install",
      "jupyter notebook",
      "pytest",
      "black",
      "flake8",
      "git status",
      "git diff"
    ],
    "deny": {
      "read": [
        "credentials.json",
        "*.key"
      ],
      "write": [
        "data/raw/**/*",
        "requirements.txt"
      ],
      "execute": [
        "rm -rf data",
        "pip uninstall"
      ]
    }
  },
  "ignoreDirs": [
    "__pycache__",
    ".ipynb_checkpoints",
    "venv",
    "env",
    ".venv"
  ]
}
```

### モバイルアプリ開発（React Native）

```json
{
  "permissions": {
    "read": [
      "src/**/*",
      "components/**/*",
      "screens/**/*",
      "navigation/**/*",
      "assets/**/*",
      "tests/**/*",
      "android/app/src/main/**/*",
      "ios/**/*.{swift,m,h}",
      "package.json",
      "app.json"
    ],
    "write": [
      "src/**/*",
      "components/**/*",
      "screens/**/*",
      "navigation/**/*",
      "assets/**/*",
      "tests/**/*"
    ],
    "execute": [
      "npm run android",
      "npm run ios",
      "npm run test",
      "npm run lint",
      "react-native start",
      "git status",
      "git diff"
    ],
    "deny": {
      "read": [".env"],
      "write": [
        "android/app/build.gradle",
        "ios/Podfile",
        "package.json"
      ],
      "execute": [
        "cd android && ./gradlew clean",
        "pod install",
        "npm publish"
      ]
    }
  },
  "ignoreDirs": [
    "node_modules",
    "android/build",
    "android/app/build",
    "ios/Pods",
    "ios/build"
  ]
}
```

---

## パフォーマンス最適化設定

### 大規模プロジェクト用

```json
{
  "permissions": {
    "read": ["src/**/*", "tests/**/*"],
    "write": ["src/**/*", "tests/**/*"],
    "execute": ["npm", "git"]
  },
  "ignoreDirs": [
    "node_modules",
    "dist",
    "build",
    ".next",
    "coverage",
    ".cache",
    "tmp",
    "temp"
  ],
  "ignorePatterns": [
    "*.log",
    "*.map",
    "*.min.js",
    "*.bundle.js"
  ],
  "maxTokensPerRequest": 16384,
  "maxFilesPerRequest": 50,
  "caching": {
    "enabled": true,
    "ttl": 3600
  }
}
```

---

## CI/CD統合設定

### GitHub Actions連携

```json
{
  "permissions": {
    "read": [
      "src/**/*",
      "tests/**/*",
      ".github/workflows/**/*"
    ],
    "write": [
      "src/**/*",
      "tests/**/*",
      ".github/workflows/**/*"
    ],
    "execute": [
      "npm run test",
      "npm run build",
      "npm run lint",
      "git status",
      "git diff",
      "git add",
      "git commit",
      "gh pr create",
      "gh pr list",
      "gh issue list"
    ]
  },
  "github": {
    "autoCreatePR": false,
    "autoReview": true,
    "branchPrefix": "claude/",
    "commitMessagePrefix": "feat: "
  }
}
```

---

## 設定のベストプラクティス

### 段階的な権限設定

```json
{
  "profiles": {
    "development": {
      "permissions": {
        "read": ["**/*"],
        "write": ["src/**/*", "tests/**/*"],
        "execute": ["npm", "git status", "git diff"]
      }
    },
    "testing": {
      "permissions": {
        "read": ["**/*"],
        "write": ["tests/**/*"],
        "execute": ["npm run test", "git status"]
      }
    },
    "production": {
      "permissions": {
        "read": ["src/**/*", "dist/**/*"],
        "write": [],
        "execute": ["git status", "git log"]
      }
    }
  },
  "activeProfile": "development"
}
```

---

## トラブルシューティング用設定

### デバッグモード有効

```json
{
  "permissions": {
    "read": ["**/*"],
    "write": ["**/*"],
    "execute": ["**/*"]
  },
  "debug": true,
  "verbose": true,
  "logLevel": "debug",
  "logFile": ".claude/debug.log"
}
```

---

## まとめ

settings.jsonは、Claude Codeの動作を細かく制御できる強力な設定ファイルです。

**推奨アプローチ:**
1. 最初は「安全重視設定」から始める
2. 必要に応じて段階的に権限を追加
3. プロジェクトの性質に合わせてカスタマイズ
4. セキュリティは常に最優先

**注意点:**
- 機密情報へのアクセスは常に deny に設定
- 破壊的なコマンドは execute から除外
- 本番環境では write 権限を最小限に

次は品質向上ルール（A3）を確認して、コードの品質をさらに高めましょう。
