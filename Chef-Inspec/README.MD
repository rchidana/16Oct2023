### Chef Inspec 

Chef InSpec is an open-source framework for testing and auditing your applications and infrastructure. <br>
Chef InSpec works by comparing the actual state of your system with the desired state that you express in easy-to-read and easy-to-write Chef InSpec code. <br>

Create a file - hello_spec.rb with the following contents:

```
control "world-1.0" do                                # A unique ID for this control
  impact 1.0                                          # Just how critical is
  title "Hello World"                                 # Readable by a human
  desc "Text should include the words 'hello world'." # Optional description
  describe file('hello.txt') do                       # The actual test
   its('content') { should match 'Hello World' }      # You could just do the "describe file" block if you want. The  
  end                                                 # rest is just metadata, but it's a good habit to get into.
end
```

Run inspec by specifying: <br>

```
inspec exec hello_spec.rb
```

Check the report and make necessary remediation for it. <br>

For reference, the following is a detailed check that can be applied for any web-application:<br>

```
# encoding: utf-8

control 'tls' do
  # Description of this test suite. Will be included in the test result report.
  title 'TLS compliance for web apps'
  desc 'Verifies that a web app complies with our published TLS standards.'
  
  # Hardcode the target app for now
  target_host = 'github.com'
  target_port = 443

  describe host(target_host, port: target_port, protocol: 'tcp') do
    it { should be_reachable }
    it { should be_resolvable }
    its('connection') { should_not match /connection refused/ }
  end
  
  # Disallow vulnerable or deprecated protocols
  describe ssl(host: target_host, port: target_port).protocols('ssl2') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).protocols('ssl3') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).protocols('tls1.0') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).protocols('tls1.1') do
    it { should_not be_enabled }
  end
  
  # Mandate strong protocols
  describe ssl(host: target_host, port: target_port).protocols('tls1.2') do
    it { should be_enabled }
  end
  
  # Disallow weak ciphers
  describe ssl(host: target_host, port: target_port).ciphers('/ANON_WITH/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/WITH_NULL/i') do
    it { should_not be_enabled }
  end
  describe ssl(host: target_host, port: target_port).ciphers('/_NULL$/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/_WITH_EXPORT/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/_WITH_RC4/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/_WITH_(\d*)(des)/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/WITH_CAMELLIA/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/_MD5$/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/_SHA$/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/^TLS_GOSTR/i') do
    it { should_not be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('/^TLS_DH/i') do
    it { should_not be_enabled }
  end
  
  # Mandate strong ciphers
  describe ssl(host: target_host, port: target_port).ciphers('TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384') do
    it { should be_enabled }
  end
  
  describe ssl(host: target_host, port: target_port).ciphers('TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256') do
    it { should be_enabled }
  end
end
```

One more sample for checking complaince for Azure cloud components: <br>

```
control 'azurerm_virtual_machine' do
  describe azurerm_virtual_machine(resource_group: resource_group, name: 'my-vm') do
    it                                { should exist }
    its('type')                       { should eq 'Microsoft.Compute/virtualMachines' }
  end
end

control 'azure_network_security_group' do
  describe azure_network_security_group(resource_group: resource_group, name: 'nsg') do
    it                            { should exist }
    its('type')                   { should eq 'Microsoft.Network/networkSecurityGroups' }
    its('security_rules')         { should_not be_empty }
    its('default_security_rules') { should_not be_empty }
    it                            { should_not allow_rdp_from_internet }
    it                            { should_not allow_ssh_from_internet }
  end
end

control 'azure_virtual_network' do
  describe azurerm_virtual_network(resource_group: resource_group, name: 'my-network') do
    it               { should exist }
    its('location')  { should eq 'centralus' }
  end
end
```
