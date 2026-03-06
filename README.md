# Terraform
Launch Linux EC2 instances in two regions using a single Terraform file.
provider "aws" {
  alias  = "east"
  region = "us-east-1"
}

provider "aws" {
  alias  = "west"
  region = "us-west-2"
}

# Get latest Amazon Linux AMI for us-east-1
data "aws_ami" "amazon_linux_east" {
  provider    = aws.east
  most_recent = true

  owners = ["amazon"]

  filter {
    name   = "name"
    values = ["al2023-ami-*-x86_64"]
  }
}

# Get latest Amazon Linux AMI for us-west-2
data "aws_ami" "amazon_linux_west" {
  provider    = aws.west
  most_recent = true

  owners = ["amazon"]

  filter {
    name   = "name"
    values = ["al2023-ami-*-x86_64"]
  }
}

resource "aws_instance" "ec2_east" {
  provider      = aws.east
  ami           = data.aws_ami.amazon_linux_east.id
  instance_type = "t3.micro"

  tags = {
    Name = "Terraform-East"
  }
}

resource "aws_instance" "ec2_west" {
  provider      = aws.west
  ami           = data.aws_ami.amazon_linux_west.id
  instance_type = "t3.micro"

  tags = {
    Name = "Terraform-West"
  }
}
