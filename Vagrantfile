

Vagrant.configure("2") do |config|

#variables
vagrantfiles = %w[vagrant.var]
vagrantfiles.each do |vagrantfile|
  load File.expand_path(vagrantfile) if File.exists?(vagrantfile)
end

# Example Ubuntu provision script
$PROVISION_UBUNTU = <<SCRIPT
uname=$(uname -a)
ver=$(lsb_release -a)
echo "== BEGIN: vagrant provisioning on '${uname}'"
echo "== UBUNTU VERSION: ${ver}"
echo "== UPDATING Ubuntu repositories and packages"
/usr/bin/apt-get update -y -qq > /dev/null 2>&1
/usr/bin/apt-get upgrade -y -qq > /dev/null 2>&1
extip=$(curl -s http://metadata/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip -H "Metadata-Flavor: Google")
echo "== EXTERNAL IP: ${extip}"
echo "== APPENDING /etc/motd"
d=$(date +%r)
echo "# ${d}" >> /etc/motd
echo "== cat /etc/motd"
cat /etc/motd
SCRIPT

    config.vm.box = "google/gce"
    config.vm.provision :shell, :inline => $PROVISION_UBUNTU
    config.vm.provider :google do |google, override|
      google.google_project_id = $GOOGLE_PROJECT_ID
      google.google_client_email = $GOOGLE_CLIENT_EMAIL
      google.google_json_key_location = $GOOGLE_JSON_KEY_LOCATION
      # Override provider defaults
      override.ssh.username = $LOCAL_USER
      override.ssh.private_key_path = $LOCAL_SSH_KEY
      google.zone = "europe-west1-b"
        google.zone_config "europe-west1-b" do |zone1f|
          zone1f.name = "testing-vagrant"
          zone1f.image = "ubuntu-1604-xenial-v20181204"
          zone1f.machine_type = "n1-standard-4"
          zone1f.zone = "europe-west1-b"
          zone1f.tags = ['web','gre']
        end
    end
end
