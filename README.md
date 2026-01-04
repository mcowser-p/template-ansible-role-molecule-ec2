# Ansible Multi-Role Repository with Molecule EC2 Testing

A comprehensive template repository for managing **multiple Ansible roles** with automated testing on AWS EC2 instances using Molecule, independent versioning per role based on conventional commits, and integrated CI/CD workflows.

## 🚀 Features

- **Multi-Role Structure** - Host multiple related roles in a single repository
- **Multi-Playbook Support** - Version individual playbooks independently
- **Independent Versioning** - Each role and playbook has its own semantic version (e.g., `webserver-v1.0.0`, `deploy-v2.1.0`)
- **Auto-Generated Changelogs** - Changelogs created automatically on first release
- **Molecule Testing** - Pre-configured Molecule scenarios for testing roles on AWS EC2 instances
- **Automated Releases** - Role and playbook-specific releases based on conventional commits with scopes
- **CI/CD Workflows** - GitHub Actions for linting, testing, and independent releases
- **Linting** - Ansible-lint and yamllint configurations
- **Documentation** - Contributing guidelines and conventional commit standards

## 📁 Repository Structure

```
.
├── .config/                      # Shared configuration files
│   ├── ansible-lint.yml         # Ansible linting rules
│   └── yamllint.yml             # YAML linting rules
│
├── .github/
│   └── workflows/               # GitHub Actions workflows
│       ├── lint-ansible.yml     # Ansible linting workflow
│       ├── lint-yaml.yml        # YAML linting workflow
│       └── release-{role}.yml   # Role-specific release workflows
│
├── roles/                       # All roles in this repository
│   ├── example-role/           # Example role
│   │   ├── defaults/
│   │   │   └── main.yml
│   │   ├── files/
│   │   ├── handlers/
│   │   │   └── main.yml
│   │   ├── meta/
│   │   │   └── main.yml
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   ├── templates/
│   │   ├── vars/
│   │   │   └── main.yml
│   │   ├── CHANGELOG.md        # Auto-generated on first release
│   │   └── README.md           # Role-specific documentation
│   │
│   └── your-role/              # Add more roles here
│       └── ...
│
├── molecule/                    # Test scenarios for each role
│   ├── example-role-ec2-linux/ # EC2 testing for example-role
│   │   ├── converge.yml
│   │   ├── molecule.yml
│   │   ├── prepare.yml
│   │   └── verify.yml
│   └── your-role-ec2-linux/    # Add test scenarios for new roles
│
├── playbooks/                   # Versioned playbooks
│   ├── example-setup.yml       # Example playbook
│   ├── your-playbook.yml       # Add more playbooks here
│   └── changelogs/             # Auto-generated on first release
│       ├── {playbook}-CHANGELOG.md
│       └── ...
│
├── docs/
│   └── CONTRIBUTING.md          # Contributing guidelines
│
├── .releaserc.{role}.yml       # Role-specific release configurations
├── .releaserc.playbook-{name}.yml  # Playbook-specific release configs
├── ansible.cfg                 # Ansible configuration
├── LICENSE                     # MIT License
├── README.md                   # This file
└── requirements.yml            # External role/collection dependencies
```

## 🎯 Quick Start

### Using This Template

1. **Create a new repository from this template:**
   - Click "Use this template" button on GitHub
   - Name your new repository (e.g., `ansible-roles-infrastructure`)
   - Clone your new repository

2. **Add your first role:**
   ```bash
   cd ansible-roles-infrastructure

   # Copy the example-role as a template
   cp -r roles/example-role roles/webserver

   # Update the role
   # - Edit roles/webserver/meta/main.yml
   # - Add tasks to roles/webserver/tasks/main.yml
   # - Define variables in roles/webserver/defaults/main.yml
   ```

3. **Commit and release:**
   ```bash
   git add .
   git commit -m "feat(webserver): initial role setup"
   git push origin main
   ```

**That's it!** The release workflow will automatically:
- Detect the new role in the `roles/` folder
- Generate a release configuration from `.releaserc.template.yml`
- Create the first release tag: `webserver-v1.0.0`

**Optional:** Create a custom release config if you need special settings:
```bash
# Copy and customize release configuration
cp .releaserc.example-role.yml .releaserc.webserver.yml
sed -i '' 's/example-role/webserver/g' .releaserc.webserver.yml
```

## 📦 Using Roles from This Repository

### Method 1: Using requirements.yml (Recommended)

Create or update your `requirements.yml` file:

```yaml
---
# requirements.yml
roles:
  # Install specific role with specific version
  - name: example-role
    src: https://github.com/yourorg/ansible-roles-infrastructure.git
    version: example-role-v1.0.0  # Role-specific version tag
    scm: git

  # Install another role with different version
  - name: webserver
    src: https://github.com/yourorg/ansible-roles-infrastructure.git
    version: webserver-v2.1.0     # Different version
    scm: git

  # Install database role
  - name: database
    src: https://github.com/yourorg/ansible-roles-infrastructure.git
    version: database-v1.5.0
    scm: git

collections:
  - name: community.general
    version: ">=5.0.0"
```

Install the roles:

```bash
# Install all requirements
ansible-galaxy install -r requirements.yml

# Force reinstall/update
ansible-galaxy install -r requirements.yml --force

# Install to a specific directory
ansible-galaxy install -r requirements.yml -p ./roles
```

### Method 2: Direct Installation from GitHub

```bash
# Install specific role at specific version
ansible-galaxy install git+https://github.com/yourorg/ansible-roles-infrastructure.git,example-role-v1.0.0

# Install with custom name
ansible-galaxy install git+https://github.com/yourorg/ansible-roles-infrastructure.git,webserver-v2.1.0 -p roles/ --force
```

### Method 3: Using in Playbooks

Once installed, use the roles in your playbooks:

```yaml
---
# playbook.yml
- name: Configure infrastructure
  hosts: all
  become: true

  roles:
    - role: example-role
      vars:
        example_var: "value"

    - role: webserver
      vars:
        webserver_port: 8080

    - role: database
      vars:
        db_name: "myapp"
```

## 🔧 Configuration Files

### ansible.cfg

The `ansible.cfg` file is pre-configured with:
- **roles_path**: `./roles` - Searches for roles in the roles directory
- **inventory**: Points to `inventory/hosts`
- **host_key_checking**: Disabled for easier testing
- **fact_caching**: Enabled with JSON file backend
- **privilege_escalation**: Configured for sudo

### Linting Configurations

#### .config/ansible-lint.yml
- Excludes cache, GitHub, test output, and molecule directories
- Allows longer YAML lines
- Permits flexible task naming
- Allows short module names for core modules

#### .config/yamllint.yml
- Line length limit: 120 characters (warning)
- 2-space indentation
- Document start required
- Ignores cache, GitHub, test, and molecule directories

## 🧪 Testing

### Molecule Testing on EC2

Each role can have its own Molecule scenario for testing on AWS EC2:

```bash
# Test a specific role on EC2
cd molecule/example-role-ec2-linux
molecule test

# Create and converge (without destroying)
molecule converge

# Run verification tests
molecule verify

# Destroy test instances
molecule destroy
```

**Required AWS Environment Variables:**
```bash
export AWS_ACCESS_KEY_ID=your_access_key
export AWS_SECRET_ACCESS_KEY=your_secret_key
export AWS_REGION=us-east-1
```

## 📝 Conventional Commits for Multi-Role Repository

This template uses [Conventional Commits](https://www.conventionalcommits.org/) with **role-specific scopes** for independent releases.

### Commit Format

```
<type>(<role-name>): <description>

[optional body]

[optional footer(s)]
```

### Examples

```bash
# Release example-role v1.1.0 (minor)
git commit -m "feat(example-role): add support for Ubuntu 24.04"

# Release webserver v1.0.1 (patch)
git commit -m "fix(webserver): correct SSL certificate path"

# Release database v2.0.0 (major - breaking change)
git commit -m "feat(database)!: change default port to 5433

BREAKING CHANGE: Default PostgreSQL port changed from 5432 to 5433"

# No release (affects repository, not a specific role)
git commit -m "docs: update main README"
git commit -m "chore: update CI configuration"

# Multiple roles (will trigger releases for both)
git commit -m "fix(webserver): update nginx config
fix(database): update backup script"
```

### Release Types by Scope

- `feat(role-name):` - New feature (minor version bump for that role)
- `fix(role-name):` - Bug fix (patch version bump for that role)
- `perf(role-name):` - Performance improvement (patch version bump)
- `refactor(role-name):` - Code refactoring (patch version bump)
- `docs(role-name):` - Documentation changes (patch version bump)
- `BREAKING CHANGE:` or `!` - Breaking change (major version bump)

### Version Tags

Each role gets its own version tag:
- `example-role-v1.0.0`
- `example-role-v1.1.0`
- `webserver-v1.0.0`
- `webserver-v2.0.0`
- `database-v1.5.0`

## 🔐 GitHub Secrets

Configure these secrets in your GitHub repository for CI/CD:

- `AWS_ACCESS_KEY_ID` - AWS access key for Molecule testing
- `AWS_SECRET_ACCESS_KEY` - AWS secret key for Molecule testing
- `AWS_REGION` - AWS region (e.g., us-east-1)

## 📖 Adding a New Playbook

### Quick Method (Automatic Release Config)

1. **Create the playbook file:**
   ```bash
   # Create your playbook in the playbooks directory
   cat > playbooks/webserver-deploy.yml << 'EOF'
   ---
   - name: Deploy webserver
     hosts: webservers
     become: true

     tasks:
       - name: Install nginx
         ansible.builtin.package:
           name: nginx
           state: present
   EOF
   ```

2. **Commit and push:**
   ```bash
   git add .
   git commit -m "feat(webserver-deploy): initial playbook"
   git push origin main
   ```

**Done!** The release workflow will:
- Automatically detect the new playbook
- Generate release config from `.releaserc.playbook-template.yml`
- Create changelog at `playbooks/changelogs/webserver-deploy-CHANGELOG.md`
- Create the first release: `webserver-deploy-v1.0.0`

### Using Playbooks

**Clone specific playbook version:**
```bash
# Clone the repository at a specific playbook version
git clone --branch webserver-deploy-v1.0.0 https://github.com/yourorg/repo.git
cd repo
ansible-playbook playbooks/webserver-deploy.yml -i inventory/hosts
```

**Conventional commits for playbooks:**
```bash
# Release webserver-deploy v1.1.0 (minor)
git commit -m "feat(webserver-deploy): add SSL configuration"

# Release database-backup v1.0.1 (patch)
git commit -m "fix(database-backup): correct backup path"

# Release monitoring-setup v2.0.0 (major)
git commit -m "feat(monitoring-setup)!: change to Prometheus

BREAKING CHANGE: Switched from Nagios to Prometheus"
```

### Playbook Version Tags

Each playbook gets its own version tag:
- `webserver-deploy-v1.0.0`
- `database-backup-v2.1.0`
- `monitoring-setup-v1.5.0`

## 📚 Adding a New Role

### Quick Method (Automatic Release Config)

1. **Create the role directory:**
   ```bash
   # Copy example-role as template
   cp -r roles/example-role roles/your-new-role

   # Update role metadata
   vim roles/your-new-role/meta/main.yml
   vim roles/your-new-role/README.md
   ```

2. **Commit and push:**
   ```bash
   git add .
   git commit -m "feat(your-new-role): initial role setup"
   git push origin main
   ```

**Done!** The release workflow will:
- Automatically detect the new role
- Generate release config from `.releaserc.template.yml`
- Create the first release: `your-new-role-v1.0.0`

### Advanced Method (Custom Release Config)

If you need custom release settings:

1. **Create role and custom config:**
   ```bash
   # Copy role
   cp -r roles/example-role roles/your-new-role

   # Create custom release config
   cp .releaserc.example-role.yml .releaserc.your-new-role.yml
   sed -i '' 's/example-role/your-new-role/g' .releaserc.your-new-role.yml

   # Customize .releaserc.your-new-role.yml as needed
   ```

2. **Optional: Create Molecule test scenario:**
   ```bash
   cp -r molecule/example-role-ec2-linux molecule/your-new-role-ec2-linux
   # Update molecule/your-new-role-ec2-linux/molecule.yml
   ```

3. **Commit and push:**
   ```bash
   git add .
   git commit -m "feat(your-new-role): initial role setup"
   git push origin main
   ```

### How It Works

The `release-roles.yml` workflow:
1. **Detects** all roles in the `roles/` directory
2. **Checks** for role-specific changes in commits
3. **Uses** existing `.releaserc.{role}.yml` if present
4. **Generates** config from `.releaserc.template.yml` if not
5. **Creates** independent version tags per role

## 🤝 Contributing

See [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md) for detailed contribution guidelines.

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🆘 Support

- **Issues**: Report bugs or request features via GitHub Issues
- **Discussions**: Ask questions in GitHub Discussions
- **Documentation**: Check the [docs/](docs/) directory and individual role READMEs

## 🔗 Resources

- [Ansible Documentation](https://docs.ansible.com/)
- [Molecule Documentation](https://molecule.readthedocs.io/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
- [Ansible Galaxy](https://galaxy.ansible.com/)

---

**Happy Automating! 🚀**
